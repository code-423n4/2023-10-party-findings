# [L-01] `initialContribution` is not correctly calculated in `InitialETHCrowdfund.sol`

[File: InitialETHCrowdfund.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L138-L140)
```
        // If the deployer passed in some ETH during deployment, credit them
        // for the initial contribution.
        uint96 initialContribution = msg.value.safeCastUint256ToUint96()
```

According to above comment, `initialContribution` should take into consideration not only `msg.value`, but also the ETH which might had been sent during the deployment process. However, the current implementation does not take that pre-existing ETH into consideration. This comment is very misleading to the user and creates a risk of fund-loss (thus it was categarized as Low). User may send ETH before calling `_initialize()`, hoping that, these ETH will be added to `initialContribution`.

Our recommendation is to either calculate pre-existing ETH tokens in `initialContribution` or remove that comment.

# [L-02] It's not possible to remove previously added authority

`PartyGovernanceNFT.sol` implements only `addAuthority()` function. It allows to set any provided address as an authority. Only the party itself can add authorities.

[File: PartyGovernanceNFT.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L479)
```
function addAuthority(address authority) external onlySelf {
        isAuthority[authority] = true;

        emit AuthorityAdded(authority);
    }
```

However, there's no function which allows to remove authority. The only possible way of removing authority is calling `abdicateAuthority()`. However, this function allows to remove only own address (`msg.sender`) from the authority. In other words, if user X has authority, he/she can call `abdicateAuthority()` to remove himself/herself from the authority. However, there might be a scenario where party wants to remove user `X` from the authority, but `X` doesn't want that (thus he/she won't call `abdicateAuthority()`). In that case - it's not possible to remove `X` from authority - since protocol does not implement any function which allows removing others from authority.


# [L-03] Possible overflow in `ETHCrowdfundBase.sol`

Field `duration` is defined as `uint40`: 

[File: ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L45)
```
uint40 duration;
```

which implies, that user may set duration to any amount within a range (0, `type(uint40).max`).
Whenever user creates very long duration (so that `duration + block.timestamp > type(uint40).max)`) - the overflow will occur in the below line:

[File: ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L163)
```
expiry = uint40(block.timestamp + opts.duration);
```

Now, when expiry overflows, the crowdfund will finish immediately:

[File: ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L185)
```
if (block.timestamp >= expiry_) {
```


Since it's very unlikely that user will ever set such a huge duration - the severity of this issue has been evaluated as Low. Nonetheless, we do recommend to perform additional check which will verify if `expiry` hasn't overflown.

# [L-04] Lack of address(0) check for `authorities` in `PartyGovernanceNFT.sol`

[File: PartyGovernanceNFT.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L102-L103)
```
 for (uint256 i; i < authorities.length; ++i) {
                isAuthority[authorities[i]] = true;
```

When calling `_initialize()`, in parameter `address[] memory authorities`, we pass an array of addresses which becomes authority. There's no additional check which verifies if any of this address is address(0).


# [L-05] Lack of value 0 check for `totalVotingPower` in `_initialize()` in `PartyGovernance.sol`

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L293)
```
totalVotingPower: govOpts.totalVotingPower
```

There's no additional check which verifies if `totalVotingPower` was not set to 0 in function `_initialize()`. If it was - function `_isUnanimousVotes()` will always revert due to division by zero:

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1115)
```
uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;
```
The same division by zero revert may occur in function `_areVotesPassing()`

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1134)
```
return (uint256(voteCount) * 1e4) / uint256(totalVotingPower) >= uint256(passThresholdBps);
```

Since setting this value to 0 in `_initialize()` is unreasonable and unlikely to occur, we've marked the severity of this issue as Low.


# [L-06] Lack of validation of `fundingSplitBps` in `_initialize()` in `ETHCrowdfundBase.sol` may lead to revert due to underflow

[File: ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L168)
```
 fundingSplitBps = opts.fundingSplitBps;
```

In function `_initialize()`, there should be additional check which requires `fundingSplitBps` to be smaller than `1e4`. Otherwise, underflow may occur in following lines of code:


[File: ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L266)
```
 fundingSplitBps = opts.fundingSplitBps;
```


[File: ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L168)
```
 amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
```

[File: ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L287)
```
 amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
```

[File: ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L325)
```
 totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
```

# [L-07] Lack of casting to `uint256` may lead to overflow in `_isUnanimousVotes()` function in `PartyGovernance.sol`

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1115)
```
uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;
```

Since `acceptanceRatio` is `uint256` and `totalVotes` is `uint96`, we can safely assume, that `(totalVotes * 1e4) / totalVotingPower` will never exceed `type(uint256).max` and the result will fit in `uint256`.
However, the problem occurs with multiplication: since `totalVotes` is `uint96` - `(totalVotes * 1e4)` will also be treated as `uint96` - and this might overflow.

The safest way to avoid the risk of overflow, would be to cast `totalVotes` to `uint256` before doing the multiplication:

```
uint256 acceptanceRatio = (uint256(totalVotes) * 1e4) / totalVotingPower;
```


# [R-01] Standardize how `batchContribute()` and `batchContributeFor()` calculate `msg.value`

In `InitialETHCrowdfund.sol` there are two functions which allows to perform batch operation. Both of them, treat `msg.value` differently.

In `batchContribute()`, we assign `msg.value` to `ethAvailable` and divide it to each `votingPowers` passed as a parameter. If `msg.value` is bigger than a sum of provided `votinPowers`, we refund unused ETH:


[File: InitialETHCrowdfund.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L224)
```
if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);
```

In `batchContributeFor()`, there's no potential refund, because this function requires that the sum of `votingPowers` is exactly the same as `msg.value`:

[File: InitialETHCrowdfund.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L270-L272)
```
 if (msg.value != valuesSum) {
            revert InvalidMessageValue();
        }
```

Standardize how `batch`-like functions works. They both should either refund unused ETH (like `batchContribute()` does) or they both should require that provided sum of `votingPowers` is the same as `msg.value` (like `batchContributeFor()` does). Having similar functions which behave slightly differently, may lead to confusion to the end-user.


# [R-02] Use 2-step Host Party transfer

Whenever user provides incorrect `newPartyHost` parameter, function `abdicateHost()` will transfer party host status to incorrect address.
Since Party Host seems to be crucial role across the whole protocol design - a good idea would be to implement a 2-step Party Host transfer.

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L457)
```
function abdicateHost(address newPartyHost) external {
```

# [R-03] Define magic constant for `acceptanceRatio` in `PartyGovernance.sol`

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1119)
```
return acceptanceRatio >= 0.9999e4;
```

For better code readability, define additional constant for `0.9999e4` value.

# [R-04] Use constants which represent values, instead of hard-coding numbers

Define constant for `1e4` and `1e18`

```
PartyGovernance.sol:277:        if (govOpts.feeBps > 1e4) {
PartyGovernance.sol:280:        if (govOpts.passThresholdBps > 1e4) {
PartyGovernance.sol:1115:        uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;
PartyGovernance.sol:1134:        return (uint256(voteCount) * 1e4) / uint256(totalVotingPower) >= uint256(passThresholdBps);
ETHCrowdfundBase.sol:266:            amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
ETHCrowdfundBase.sol:270:        votingPower = (amount * exchangeRateBps) / 1e4;
ETHCrowdfundBase.sol:281:        amount = (votingPower * 1e4) / exchangeRateBps;
ETHCrowdfundBase.sol:287:            amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
ETHCrowdfundBase.sol:325:            totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
ETHCrowdfundBase.sol:329:        uint96 newVotingPower = (totalContributions_ * exchangeRateBps) / 1e4;
ETHCrowdfundBase.sol:355:        splitAmount = (totalContributions * fundingSplitBps_) / 1e4;
PartyGovernanceNFT.sol:157:            totalVotingPower == 0 ? 0 : (votingPowerByTokenId[tokenId] * 1e18) / totalVotingPower;
PartyGovernanceNFT.sol:393:                    withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
PartyGovernanceNFT.sol:413:                uint256 fee = (amount * feeBps_) / 1e4;
```

 

# [N-01] Missing reason strings in `require` statements

`Require` statements should have descriptive reason strings

[File: ProposalExecutionEngine.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/proposals/ProposalExecutionEngine.sol#L313-L314)
```
        require(proposalType != ProposalType.Invalid);
        require(uint8(proposalType) <= uint8(type(ProposalType).max));
```


# [N-02] Typos in comment

This typo was not detected during the bot-race

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L83)
```
 // Fee recipeint for distributions.
```

Should be: `Fee recipient for distributions`


# [N-03] Uncompleted comment in `InitialETHCrowdfund.sol`

[File: InitialETHCrowdfund.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L65-L66)
```
// IDs of cards to credit the contributions to. When set to 0, it means
        uint256[] tokenIds;
```

Comment in `InitialETHCrowdfund.sol` is not finished. It seems that it was a copy&paste from `BatchContributeForArgs` (lines 79-80), and the last line has been cut.
The full comment should be: `IDs of cards to credit the contributions to. When set to 0, it means a new one should be minted.`


# [N-04] Incorrect naming convention for non-constants

It's a good coding practice to use uppercase to name constants.
In `PartyGovernance.sol` and `PartyGovernanceNFT.sol` uppercase naming is used for immutable variables instead:

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L191)
```
IGlobals private immutable _GLOBALS;
```

[File: PartyGovernanceNFT.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L42)
```
IGlobals private immutable _GLOBALS;
```