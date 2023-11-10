## [L-01] Refund logic for `InitialETHCrowdfund.batchContributeFor()`
It is observed that the function enforces an exact match between the total message value (`msg.value`) and the cumulative intended contribution amounts (`valuesSum`). This strict check is positioned as a safeguard against overpayment but poses usability concerns as it can lead to transaction reversion upon the slightest excess in funds sent, predominantly hinging on user experience rather than security. It neither poses a direct threat to funds nor contract integrity but could result in an increased rate of failed transactions and unnecessary gas expenditures. Enhancing this function with a refund mechanism, akin to the existing `batchContribute` method, is recommended to mitigate these concerns, provided such alterations are meticulously tested to preclude potential security vulnerabilities.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L255-L273

```diff
    function batchContributeFor(
        BatchContributeForArgs calldata args
    ) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {
        votingPowers = new uint96[](args.recipients.length);
        uint256 valuesSum;
        for (uint256 i; i < args.recipients.length; ++i) {
            votingPowers[i] = _contribute(
                args.recipients[i],
                args.initialDelegates[i],
                args.values[i],
                args.tokenIds[i],
                args.gateDatas[i]
            );
            valuesSum += args.values[i];
        }
-        if (msg.value != valuesSum) {
-            revert InvalidMessageValue();
-        }
+        if (msg.value - valuesSum > 0) payable(msg.sender).transferEth(ethAvailable);
    }
```
## [L-02] Allow contribution less than `minContribution` for tokenId != 0
Increase voting power of contributor's existing party card should be allowed when the amount is less than `minContribution`. Specifically, this will make the final contribution succeed while having `totalContributions` gracefully reach `maxTotalContributions`.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L295

```diff
-        votingPower = _processContribution(contributor, delegate, amount);
+        votingPower = _processContribution(contributor, delegate, amount, tokenId);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L196-L200

```diff
    function _processContribution(
        address payable contributor,
        address delegate,
        uint96 amount
+        uint256 tokenId
    ) internal returns (uint96 votingPower) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L255-L258

```diff
        uint96 minContribution_ = minContribution;
-        if (amount < minContribution_) {
+        if (amount < minContribution_ && tokenId == 0) {
            revert BelowMinimumContributionsError(amount, minContribution_);
        }
```
## [L-03] Array length check on `InitialETHCrowdfund.batchContribute()` and `InitialETHCrowdfund.contributeFor()`
As commented in structs [`BatchContributeArgs`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L70-L71) and [`BatchContributeForArgs`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L88-L89), the length of `values` array must correspondingly be equal to the length of `tokenIds` and `recipients`.

Consider implementing array length check on `InitialETHCrowdfund.batchContribute()` and `InitialETHCrowdfund.contributeFor()` where possible.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L207

```diff
        uint256 numContributions = args.tokenIds.length;
+        if (numContributions != args.values.length) revert();
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L258

```diff
        votingPowers = new uint96[](args.recipients.length);
+        if (votingPowers != args.values.length) revert();
```
## [L-04] Unsupported `push0` on L2 chain with a version of Solidity >= 0.8.20.
According to the `Additional Context` provided on the contest page:

```
The code deployment of the protocol is targeted for two blockchains: Ethereum Mainnet and Base Mainnet.
```

`push0` is an instruction which pushes the constant value 0 onto the stack and is still not supported by many chains, like Arbitrum and possibly Base. As such, it might be problematic for projects compiled with a version of Solidity >= 0.8.20.

Consider changing Solidity version to 0.8.19 or better yet 0.8.21 on all contracts.

## [L-05] Implement early checks
Checks should be done earlier when possible. For example, all essential checks in `InitialETHCrowdfund._contribute()` should be refactored as follows: 

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L275-L311

```diff
    function _contribute(
        address payable contributor,
        address delegate,
        uint96 amount,
        uint256 tokenId,
        bytes memory gateData
    ) private returns (uint96 votingPower) {
        // Require a non-null delegate.
        if (delegate == address(0)) {
            revert InvalidDelegateError();
        }

+        if (disableContributingForExistingCard) {
+            revert ContributingForExistingCardDisabledError();
+        } else if (party.ownerOf(tokenId) != contributor) {
+            revert NotOwnerError(tokenId);
+        }

        // Must not be blocked by gatekeeper.
        IGateKeeper _gateKeeper = gateKeeper;
        if (_gateKeeper != IGateKeeper(address(0))) {
            if (!_gateKeeper.isAllowed(msg.sender, gateKeeperId, gateData)) {
                revert NotAllowedByGateKeeperError(msg.sender, _gateKeeper, gateKeeperId, gateData);
            }
        }

        votingPower = _processContribution(contributor, delegate, amount);

        // OK to contribute with zero just to update delegate.
        if (amount == 0) return 0;

        if (tokenId == 0) {
            // Mint contributor a new party card.
            party.mint(contributor, votingPower, delegate);
-        } else if (disableContributingForExistingCard) {
-            revert ContributingForExistingCardDisabledError();
-        } else if (party.ownerOf(tokenId) == contributor) {
+        } else
            // Increase voting power of contributor's existing party card.
            party.increaseVotingPower(tokenId, votingPower);
-        } else {
-            revert NotOwnerError(tokenId);
-        }
    }
```
## [L-06] Trace Eth amount left in Crowdfund contract
Due to the Solidity language's integer division, which truncates fractional remainders, there will be trace amount of Eth left in the contract after the a won crowdfund has been finalized. Consider refactoring the function below to facilitate a clean sweep of funds:  

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L338-L359

```diff
    /// @notice Send the funding split to the recipient if applicable.
    function sendFundingSplit() external returns (uint96 splitAmount) {
        // Check that the crowdfund is finalized.
        CrowdfundLifecycle lc = getCrowdfundLifecycle();
        if (lc != CrowdfundLifecycle.Finalized) revert WrongLifecycleError(lc);

        if (fundingSplitPaid) revert FundingSplitAlreadyPaidError();

        address payable fundingSplitRecipient_ = fundingSplitRecipient;
        uint16 fundingSplitBps_ = fundingSplitBps;
        if (fundingSplitRecipient_ == address(0) || fundingSplitBps_ == 0) {
            revert FundingSplitNotConfiguredError();
        }

        fundingSplitPaid = true;

        // Transfer funding split to recipient.
-        splitAmount = (totalContributions * fundingSplitBps_) / 1e4;
-        payable(fundingSplitRecipient_).transferEth(splitAmount);
+        payable(fundingSplitRecipient_).transferEth(address.(this).balance);

        emit FundingSplitSent(fundingSplitRecipient_, splitAmount);
    }
```
Note: The same should also apply to contribution amount if the voting duration expired and the minimum contribution target was not reached. I have nonetheless submitted this separately as a medium finding due to the cumulative four-fold truncations on each user.

## [L-07] Private function with embedded modifier reduces contract size
According to `Changelog` on the contest page:

```
Update to inline modifiers to reduce contract size for Party :: Necessary to meet the contract size limit.
```
While the above method works, the trade off is giving up the useful modifiers.

Consider having the logic of each modifier embedded through a private function to accomplish the same goal of reducing the contract size while keeping the modifier. A `private` visibility that saves more gas on function calls than the `internal` visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows:

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L231-L239

```diff
+    function _onlyPartyDao() private view {
+        {
+            address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
+            if (msg.sender != partyDao) {
+                revert NotAuthorized();
+            }
+        }
+    }

  modifier onlyPartyDao() {
-        {
-            address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
-            if (msg.sender != partyDao) {
-                revert NotAuthorized();
-            }
-        }
+        _onlyPartyDao();
      _;
  }
```
## [L-08] Test address in PartyGovernanceNFT.sol
Testnet address for PartyGovernanceNFT.sol is used in the code:

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L38

```solidity
    address private constant ETH_ADDRESS = 0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE;
```
According to the Additional Context provided on the contest page:

```
The code deployment of the protocol is targeted for two blockchains: Ethereum Mainnet and Base Mainnet.
```
Remember to make the changes necessary to be compatible with Ethereum Mainnet and Base Mainnet.

## [L-09] Prevent making proposal before the party is formed
Prevent creating a proposal if the party has not started by having `propose()` refactored as follows:

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L553-L582

```diff
    function propose(
        Proposal memory proposal,
        uint256 latestSnapIndex
    ) external returns (uint256 proposalId) {
        _assertActiveMember();

+        uint96 totalVotingPower_ = _getSharedProposalStorage().governanceValues.totalVotingPower
+        if (totalVotingPower_ == 0) {
+            revert PartyNotStartedError();
+        }

        proposalId = ++lastProposalId;
        // Store the time the proposal was created and the proposal hash.
        (
            _proposalStateByProposalId[proposalId].values,
            _proposalStateByProposalId[proposalId].hash
        ) = (
            ProposalStateValues({
                proposedTime: uint40(block.timestamp),
                passedTime: 0,
                executedTime: 0,
                completedTime: 0,
                votes: 0,
-                totalVotingPower: _getSharedProposalStorage().governanceValues.totalVotingPower,
+                totalVotingPower: totalVotingPower_,
                numHosts: numHosts,
                numHostsAccepted: 0
            }),
            getProposalHash(proposal)
        );
        emit Proposed(proposalId, msg.sender, proposal);
        accept(proposalId, latestSnapIndex);

        // Notify third-party platforms that the governance NFT metadata has
        // updated for all tokens.
        emit BatchMetadataUpdate(0, type(uint256).max);
    }
```
## [L-10] A more robust proposal cycle
Consider implementing the following:

1. A transient time or window after a proposal is submitted before members can start voting to better facilitate filtering via veto by the hosts. This will make each proposal in its `Voting` state more intact and established rather than abruptly vetoed midway to the `Passed` and/or `Ready` state, incentivizing more members to participate.   
2. A threshold intrinsic voting power to qualify a member submitting a proposal to further circumvent spamming.
3. Against votes via `reject()` so that members opposing the proposal could take actions rather than just abstaining from calling `accept()`. 

## [NC-01] Incomplete comment
Consider inserting the missing comment below on struct `BatchContributeArgs` in contract `InitialETHCrowdfund`. 

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L66-L67

```diff
        // IDs of cards to credit the contributions to. When set to 0, it means
+        // a new one should be minted.
        uint256[] tokenIds;
```
## [NC-02] Incorrect comments
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L201
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L252

```diff
-    ///         May not revert if any individual contribution fails.
+    ///         May revert if any individual contribution fails.
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L346

```diff
-    ///         May not revert if any individual refund fails.
+    ///         May revert if any individual refund fails.
``` 
## [NC-03] Spelling/grammatical mistakes
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L87

```diff
-    // is immutable and it’s address will never change.
+    // is immutable and its address will never change.
```