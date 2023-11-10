## [QA-1] `rageQuit()` may result in minor loss due to rounding when withdrawing ERC20s with small number of `decimals()`

**Example secnario:**

ERC20: Gemini USD (GUSD)(2 decimals)
Party balance of Gemini USD = 900.00 GUSD
User contribution = 0.9 ETH
Exchange rate (bps) = 0.01% (100ETH = 1 voting token)

This results in the following calculation for the withdrawal amount in `rageQuit()`:

`withdrawAmount = (900e2 * 0.1e14) / 1e18`

which is rounded to zero, i.e. results in 0.9 GUSD loss for the user.

**Recommendation:**

There are different possible solutions and it's more a product decision for the protocol:
- Round up instead of rounding down
- Throw an error if `withdrawAmount` ends up being `0`

## [QA-2] `voteDuration` validation should be consistent across the application

In some places, validation is performed against the `voteDuration` parameter (e.g. in `SetGovernanceParameterProposal`) while in all other parts of the code, no validation is performed against it which leads to inconsistencies.

**Recommendation:*
Make sure validation rules for any particular parameter is consistent across the whole protocol.

## [QA-3] Missing zero-address check for `fundingSplitRecipient` when `fundingSplitBps` is non-zero in `ETHCrowdfundBase#_initialize()`

When `fundingSplitBps` is non-zero, it implies that the crowdfund intends to have a funding split. But no zero-address check is added for `fundingSplitRecipient` in that case in `ETHCrowdfundBase#_initialize()`.

This results in the funding split feature being disabled for the crowdfund without any possibility for configuring it later on which may be undesirable.

**Recommendation:**

In `ETHCrowdfundBase#_initialize()` add zero-address check for `fundingSplitRecipient` if `fundingSplitBps > 0`:

```diff
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -166,6 +166,9 @@ contract ETHCrowdfundBase is Implementation {
         exchangeRateBps = opts.exchangeRateBps;
         // Set the funding split and its recipient.
         fundingSplitBps = opts.fundingSplitBps;
+        if (opts.fundingSplitBps > 0 && opts.fundingSplitRecipient == address(0)) {
+            revert InvalidFundingSplitRecipient();
+        }
         fundingSplitRecipient = opts.fundingSplitRecipient;
         // Set whether to disable contributing for existing card.
         disableContributingForExistingCard = opts.disableContributingForExistingCard;
```

## [QA-4] `ETHCrowdfundBase` must be an abstract contract

It seems there is no need to instantiate any `ETHCrowdfundBase` contracts directly thus it's best to mark it as `abstract`.

**Recommendation:**

Mark `ETHCrowdfundBase` as `abstract`:

```diff
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -6,7 +6,7 @@ import "../utils/LibSafeCast.sol";
 import "../party/Party.sol";
 import "../gatekeepers/IGateKeeper.sol";

-contract ETHCrowdfundBase is Implementation {
+abstract contract ETHCrowdfundBase is Implementation {
     using LibRawResult for bytes;
     using LibSafeCast for uint256;
     using LibAddress for address payable;
```

## [QA-5] Misleading natspec for `contribute()` in `InitialETHCrowdfund`

The natspec says:

> Contribute ETH to this crowdfund on behalf of a contributor.

The term on behalf of implies that contributes are made as a representative of someone else. Which is not the case here since the contributor here is `msg.sender` themselves.

**Recommendation:**

Update the natspec so it reflects what the function actually does.

## [QA-6] `maxContribution` inside `ETHCrowdfundBase.sol` is not honored

The value does nothing. It does not restrict the maximum number of contributions by an address in any way.

Example: if `maxContribution = 10 ether`, and someone wants to deposit `20 ether`, they will just contribute 2 times instead. Which will only consume more gas but this would not prevent the user from contributing as much as they want.`

**Recommendation:**

Consider implementing a check similar to the one in `Crowdfund` which takes into consideration all previous contributions by the user:

```sol
uint256 numContributions = contributions.length;
uint96 ethContributed;
for (uint256 i; i < numContributions; ++i) {
    ethContributed += contributions[i].amount;
}
// ...
if (ethContributed + amount > maxContribution) {
    revert AboveMaximumContributionsError(ethContributed + amount, maxContribution);
}
```

## [QA-7] Missing check for array length equality in `InitialETHCrowdfund#batchContribute()`

There is no check if the passed arrays are of equal length. This may lead to mistakes and incorrect and unintended distribution of funds. These issues may be easily avoided by adding array length equality check.

**Recommendation:**

Make sure all arrays passed to `batchContribute()` are of equal length:

```diff
--- a/contracts/crowdfund/InitialETHCrowdfund.sol
+++ b/contracts/crowdfund/InitialETHCrowdfund.sol
@@ -205,6 +205,13 @@ contract InitialETHCrowdfund is ETHCrowdfundBase {
         BatchContributeArgs calldata args
     ) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {
         uint256 numContributions = args.tokenIds.length;
+        uint256 numValues = args.values.length;
+        uint256 numGateDatas = args.gateDatas.length;
+
+        if (numContributions != numValues || (numGateDatas > 0 && numContributions != numGateDatas)) {
+            revert InvalidBatchContributeArgs();
+        }
+
         votingPowers = new uint96[](numContributions);

         uint256 ethAvailable = msg.value;
```


## [QA-8] Superfluous check inside `PartyGovernanceNFT.sol#abdicateaAuthority()`

```sol
function abdicateAuthority() external {
        _assertAuthority(); // 👈 @audit Superfluous check
        delete isAuthority[msg.sender];

        emit AuthorityRemoved(msg.sender);
    }
```

If the caller isn't an authority and calls the function, nothing will change.

**Recommendation:**

Remove the `_assertAuthority()` check in `PartyGovernanceNFT#abdicateaAuthority()`

## [QA-9] `PartyDelegateUpdated` event is erroneously emitted in `PartyGovernance#_adjustVotingPower()`, even when there is no actual update to the delegate

This unintended event emission could potentially disrupt external systems that depend on the protocol's events as a messaging mechanism.

**Recommendation:**

Make sure the event is only emitted when the delegate is actually updated.