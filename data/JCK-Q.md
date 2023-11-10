
## LOW Risk

| Number | Issue | Instances |
|--------|-------|-----------|
|[L-01]| Comments state that pre-existing ETH can be used but it can’t  | 1 |
|[L-02]| Use delegationsByContributor[contributor] instead of delegate when minting party card | 1 |
|[L-03]| Use uint256 for computations such that voting power can be all values in uint96 range  | 2 |
|[L-04]| Validate rageQuitTimestamp in PartyGovernanceNFT._initialize() | 1 |
|[L-05]| Functions calling contracts with transfer hooks are missing reentrancy guards  | 1 |
|[L-06]| Loss of precision in divisions  | 2 |
|[L-07]| Execution at deadlines should be allowed  | 3 |
|[L-08]| lack of Authorization Control  | 1 |

## [L-01] Comments state that pre-existing ETH can be used but it can’t

It is possible to provide an initial contribution to the InitialETHCrowdfund contract.

The initial contribution is processed when the initialize function is called.

In  contracts it is stated that pre-existing ETH is used for the initial contribution (i.e. ETH that is owned by the contract but not sent along with the call to the initialize function):


However the initial contribution is only measured by looking at msg.value:

```solidity
file:  main/contracts/crowdfund/InitialETHCrowdfund.sol

138          // If the deployer passed in some ETH during deployment, credit them
        // for the initial contribution.
        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
        if (initialContribution > 0) {
            // If this contract has ETH, either passed in during deployment or
            // pre-existing, credit it to the `initialContributor`.
            _contribute(
                crowdfundOpts.initialContributor,
                crowdfundOpts.initialDelegate,
                initialContribution,
                0,
                ""
            );
        }


```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L138-L151


This means that pre-existing ETH is not actually processed and just sits in the contract without being used for anything. It can’t even be rescued.

It was assessed with the sponsor that they don’t actually want to allow pre-existing ETH to be used for the initial contribution.

Therefore the comments should be removed so users don’t make a mistake and lose their ETH.

### Recommanded code 

```solidity

diff --git a/contracts/crowdfund/InitialETHCrowdfund.sol b/contracts/crowdfund/InitialETHCrowdfund.sol
index 8ab3b5c..bcc65e2 100644
--- a/contracts/crowdfund/InitialETHCrowdfund.sol
+++ b/contracts/crowdfund/InitialETHCrowdfund.sol
@@ -115,12 +115,9 @@ contract InitialETHCrowdfund is ETHCrowdfundBase {
             })
         );
 
-        // If the deployer passed in some ETH during deployment, credit them
-        // for the initial contribution.
         uint96 initialContribution = msg.value.safeCastUint256ToUint96();
         if (initialContribution > 0) {
-            // If this contract has ETH, either passed in during deployment or
-            // pre-existing, credit it to the `initialContributor`.
+            // credit msg.value to the `initialContributor`.
             _contribute(
                 crowdfundOpts.initialContributor,
                 crowdfundOpts.initialDelegate,
```


## [L-02] Use delegationsByContributor[contributor] instead of delegate when minting party card

The ÌnitialETHCrowdfund._contribute function currently uses delegate as the delegate when minting a party card.

```solidity
file:  main/contracts/crowdfund/InitialETHCrowdfund.sol

302     party.mint(contributor, votingPower, delegate);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L302

This is wrong. Instead delegationsByContributor[contributor] should be used. This is because if User A contributes for another User B and User B has already set a delegate, i.e. delegationsByContributor[contributor]!=address(0) then this delegate should be used and not the delegate parameter supplied by User A.

However I don’t see any impact in this behavior because if delegationsByContributor[contributor]!=address(0) then the party also has a delegate set for the contributor. And so this delegate is used above the delegate from the crowdfund anyway. So this finding is Low severity at most.


### Recommanded code 
```solidity
diff --git a/contracts/crowdfund/InitialETHCrowdfund.sol b/contracts/crowdfund/InitialETHCrowdfund.sol
index 8ab3b5c..6c76e88 100644
--- a/contracts/crowdfund/InitialETHCrowdfund.sol
+++ b/contracts/crowdfund/InitialETHCrowdfund.sol
@@ -299,7 +299,7 @@ contract InitialETHCrowdfund is ETHCrowdfundBase {
 
         if (tokenId == 0) {
             // Mint contributor a new party card.
-            party.mint(contributor, votingPower, delegate);
+            party.mint(contributor, votingPower, delegationsByContributor[contributor]);
         } else if (disableContributingForExistingCard) {
             revert ContributingForExistingCardDisabledError();
         } else if (party.ownerOf(tokenId) == contributor) {

```


## [L-03] Use uint256 for computations such that voting power can be all values in uint96 range

The voting power in a party is managed in uint96 variables.

When we look at the PartyGovernance._areVotesPassing function we can see that for the computation the uint96 variables are cast to uint256:


```solidity
file:

1037  function _isUnanimousVotes(
        uint96 totalVotes,
        uint96 totalVotingPower
    ) private pure returns (bool) {
        uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;
        // If >= 99.99% acceptance, consider it unanimous.
        // The minting formula for voting power is a bit lossy, so we check
        // for slightly less than 100%.
        return acceptanceRatio >= 0.9999e4;
    }


```
https://github.com/code-423n4/2023-04-party/blob/440aafacb0f15d037594cebc85fd471729bcb6d9/contracts/party/PartyGovernance.sol#L1037-L1046

This is done such that there is no intermediate overflow. If there was no cast, the multiplication voteCount * 1e4 could overflow and cause a DOS to the Party when voteCount is close to type(uint96).max = ~ 7.9e28.

The issue is in the PartyGovernance._isUnanimousVotes function which does not convert the voting power to uint256 and is therefore prone to overflow:


```solidity
file:

1048  function _areVotesPassing(
        uint96 voteCount,
        uint96 totalVotingPower,
        uint16 passThresholdBps
    ) private pure returns (bool) {
        return (uint256(voteCount) * 1e4) / uint256(totalVotingPower) >= uint256(passThresholdBps);
    }

```
https://github.com/code-423n4/2023-04-party/blob/440aafacb0f15d037594cebc85fd471729bcb6d9/contracts/party/PartyGovernance.sol#L1048-L1054


As long as the mint authorities ensure that the number of votes stays within the safe range (7.9e28 / 1e4 = ~7.9e24) this is not a poblem. However the way the _areVotesPassing function works shows that the whole uint96 range should be safe.

Therefore I propose the following change to the _isUnanimousVotes function:

```solidity
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index e251646..7571fa8 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -1038,7 +1038,7 @@ abstract contract PartyGovernance is
         uint96 totalVotes,
         uint96 totalVotingPower
     ) private pure returns (bool) {
-        uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;
+        uint256 acceptanceRatio = (uint256(totalVotes) * 1e4) / uint256(totalVotingPower);
         // If >= 99.99% acceptance, consider it unanimous.
         // The minting formula for voting power is a bit lossy, so we check
         // for slightly less than 100%.
```

## [L-04] Validate rageQuitTimestamp in PartyGovernanceNFT._initialize()

rageQuit() is a valuable feature for Party members since it is a protective measure for them to be able to do an emergency withdrawal of their assets. Given that, it would be a good and sane default for rageQuitTimestamp to be initialized to some value in the future so that Party members are assured they can rageQuit in the early stages of the Party.

```solidity
file:

101   rageQuitTimestamp = rageQuitTimestamp_;

```
https://github.com/code-423n4/2023-05-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L101

## [L-05] Functions calling contracts with transfer hooks are missing reentrancy guards

Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks will open the users of this protocol up to read-only reentrancies with no way to protect against it, except by block-listing the whole protocol.

### Proof of Concept
The PartyGovernance contract inherits from OpenZeppelin’s ReentrancyGuard contract and has marked most of its state-changing methods that do ERC20 external calls with the nonReentrant modifier. The problem is the modifier is missing on some of the functions that also do ERC20 external calls - the distribute methods. Currently the methods are not exploitable, but ERC777 tokens (which are ERC20 compatible) can reenter a method call because of their pre and post hooks, so the nonReentrant modifier is an important security measure when doing unsafe ERC20 external calls.

### Impact
If ERC777 tokens were used as rewardToken or underlying they can reenter the ddistribute methods, which currently is not exploitable, but might become a big problem when new code is added.



```solidity
file: main/contracts/party/PartyGovernance.sol

534   IERC20(token).compatTransfer(address(distributor), amount);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L534


### Recommendation
Add the nonReentrant modifier to the ddistribute methods

## [L‑06] Loss of precision in divisions

Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator.

```solidity
file: contracts/crowdfund/ETHCrowdfundBase.sol

287   amount = (amount * 1e4) / (1e4 - fundingSplitBps_);

```
https://code4rena.com/contests/contracts/crowdfund/ETHCrowdfundBase.sol#L287


```solidity
file:  contracts/party/PartyGovernance.sol

434    uint256 mid = (low + high) / 2;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L434


## [L-07] Execution at deadlines should be allowed

The condition may be wrong in these cases, as when block.timestamp is equal to the compared > or < variable these blocks will not be executed.

```solidity
file: contracts/party/PartyGovernance.sol

731   if (proposal.maxExecutableTime < block.timestamp) {

811   if (block.timestamp < cancelTime) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L731


```solidity
file:  contracts/party/PartyGovernanceNFT.sol

359    if (
          currentRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY ||
          currentRageQuitTimestamp < block.timestamp
         ) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L359-L362


## [L-08] lack of Authorization Control

Description:
Authorization control risk is a vulnerability in a smart contract where proper authorization and permission checks are not enforced before allowing certain critical operations. In the context of the provided mint function, the risk is that there is no check for a valid user before minting voting power and delegating voting power. The _assertAuthority() function is supposed to handle authorization, but if it is not correctly implemented or if it fails to execute, unauthorized users could potentially mint voting power and delegate voting power, which could undermine the intended security and governance mechanisms of the smart contract.


```solidity
file: contracts/party/PartyGovernanceNFT.sol

191    function mint(
        address owner,
        uint256 votingPower,
        address delegate
    ) external returns (uint256 tokenId) {
        _assertAuthority();
        uint96 mintedVotingPower_ = mintedVotingPower;
        uint96 totalVotingPower = _getSharedProposalStorage()
            .governanceValues
            .totalVotingPower;

        // Cap voting power to remaining unminted voting power supply.
        uint96 votingPower_ = votingPower.safeCastUint256ToUint96();
        // Allow minting past total voting power if minting party cards for
        // initial crowdfund when there is no total voting power.
        if (
            totalVotingPower != 0 &&
            totalVotingPower - mintedVotingPower_ < votingPower_
        ) {
            unchecked {
                votingPower_ = totalVotingPower - mintedVotingPower_;
            }
        }

        // Update state.
        unchecked {
            tokenId = ++tokenCount;
        }
        mintedVotingPower += votingPower_;
        votingPowerByTokenId[tokenId] = votingPower_;

        emit PartyCardIntrinsicVotingPowerSet(tokenId, votingPower_);

        // Use delegate from party over the one set during crowdfund.
        address delegate_ = delegationsByVoter[owner];
        if (delegate_ != address(0)) {
            delegate = delegate_;
        }

        _adjustVotingPower(
            owner,
            votingPower_.safeCastUint96ToInt192(),
            delegate
        );
        _safeMint(owner, tokenId);
    }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L166-L230

Impact:
Unauthorized users could mint voting power and delegate it, potentially leading to misuse of the system and disrupting its intended operation.
This risk can compromise the integrity and fairness of the governance or voting system associated with the smart contract.
It could result in a loss of trust among users and stakeholders in the smart contract.


