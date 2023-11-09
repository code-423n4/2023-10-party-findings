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