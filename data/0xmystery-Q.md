## Refund logic for `InitialETHCrowdfund.batchContributeFor()`
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
## Allow contribution less than `minContribution` for tokenId != 0
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
## Array length check on `InitialETHCrowdfund.batchContribute()` and `InitialETHCrowdfund.contributeFor()`
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
## Unsupported `push0` on L2 chain with a version of Solidity >= 0.8.20.
According to the `Additional Context` provided on the contest page:

```
The code deployment of the protocol is targeted for two blockchains: Ethereum Mainnet and Base Mainnet.
```

`push0` is an instruction which pushes the constant value 0 onto the stack and is still not supported by many chains, like Arbitrum and possibly Base. As such, it might be problematic for projects compiled with a version of Solidity >= 0.8.20.

Consider changing Solidity version to 0.8.19 or better yet 0.8.21 on all contracts.

## Incomplete comment
Consider inserting the missing comment below on struct `BatchContributeArgs` in contract `InitialETHCrowdfund`. 

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L66-L67

```diff
        // IDs of cards to credit the contributions to. When set to 0, it means
+        // a new one should be minted.
        uint256[] tokenIds;
```
## Incorrect comments
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
## Implement early checks
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
## Trace Eth amount left in InitialETHCrowdfund.sol
