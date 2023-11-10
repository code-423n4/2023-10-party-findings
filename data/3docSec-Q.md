# [L-01] Several InitialETHCrowdfund and ETHCrowdfundBase functions can be called without passing by a proxy

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L318
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L350

The InitialETHCrowdfund contract is meant to be used through a proxy, and many of its methods are decorated with the `onlyDelegateCall` modifier. However, several others don't, so they can be called directly.
Also, the Party contract and its parents are supposed to be used via a proxy, but they don't implement any `onlyDelegateCall` modifier.

Consider adding:
- the `onlyDelegateCall` modifier to:
  - `InitialETHCrowdfund.batchRefund`
  - `ETHCrowdfundBase.finalize`
  - `ETHCrowdfundBase.sendFundingSplit`
  - `ETHCrowdfundBase.emergencyExecute`
  - `ETHCrowdfundBase.disableEmergencyExecute`
  - all external methods of `PartyGovernance` and `PartyGovernanceNFT`
- an ad-hoc modifier that allows delegate calls and regular calls where `msg.sender == address(this)` to `refund` to allow multicalls

Note that this applies to several initializers too, but this is reported separately with higher impact.

---

# [L-02] InitialETHCrowdfund's initial contribution does not allow passing "gateData"

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L149

At initialization time, `InitialETHCrowdfund` allows for the caller to provide an initial contribution.

Unlike other contributions, however, the initial contribution doesn't allow passing a `gateData` parameter for the gateKeeper, so initial contributions may fail for any `InitialETHCrowdfund` instance linked to a gatekeeper that requires this data to validate the contribution.

```Solidity
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
```

Consider adding a `gateData` field to the `InitialETHCrowdfundOptions` structure to be passed instead of an empty memory portion.

---

# [L-03] Owner-approved addresses are not allowed to contribute to existing PartyGovernanceNFT tokens during crowdfunding

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L305

With the current implementation of PartyGovernanceNFT, approved addresses have extreme power over the NFTs they are approved for: they can `transfer` the approved NFTs, but also burn them from `rageQuit` without owner interaction.

They are however not allowed to contribute to them:

```Solidity
        // InitialETHCrowdfund.sol:305
        } else if (party.ownerOf(tokenId) == contributor) {
            // Increase voting power of contributor's existing party card.
            party.increaseVotingPower(tokenId, votingPower);
        } else {
            revert NotOwnerError(tokenId);
        }
```

Consider allowing `approved` and `approvedForAll` addresses to contribute existing NFTs.

---

# [L-04] Bps parameters are not validated to not exceed 1e4

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L165
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L168

The protocol accepts configuration parameters in Bps (that are implicitly bound between 0 - 0% and 10_000 - 100%). While the lower bound is validated by the usage of an unsigned type, the higher bound is not validated. 

Values equal or above 1e4 of these two configurations are accepted and can have a large impact on the protocol, possibly beyond mere availability.

Consider adding checks for these values to not exceed a reasonable caps, most likely exclusive of 1e4 that is problematic already for zero divisions.

---

# [L-05] OffChainSignatureValidator.isValidSignature may revert for division by zero for not started parties

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/signature-validators/OffChainSignatureValidator.sol#L71

Whenever an OffChainSignatureValidator is configured with a non-zero signing threshold for the party (`thresholdBps > 0`), but the party has not started yet (`totalVotingPower == 0`), the following calculation in `isValidSignature` may revert for division by zero:

```Solidity
        // OffChainSignatureValidator.sol:71
        if (
            thresholdBps == 0 ||
            (signerVotingPowerBps > totalVotingPower &&
                signerVotingPowerBps / totalVotingPower >= thresholdBps)
        ) {
            return IERC1271.isValidSignature.selector;
        }
```

Consider re-formulating the operation in a way that would always work:

```diff
        // OffChainSignatureValidator.sol:71
        if (
            thresholdBps == 0 ||
            (signerVotingPowerBps > totalVotingPower &&
-               signerVotingPowerBps / totalVotingPower >= thresholdBps)
+               signerVotingPowerBps >= thresholdBps * totalVotingPower)
        ) {
            return IERC1271.isValidSignature.selector;
        }
```

---

# [L-06] Contributed tokens left over from exchangeRateBps and fundingSplitBps floor rounding are not reimbursed

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L302
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L267
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L270
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L329

When a user contributes to an `InitialETHCrowdfund`, the native tokens they send are stored and converted as shares in a `PartyGovernanceNFT`, scaled down with the `exchangeRateBps` and `fundingSplitBps` factors:

```Solidity
        // InitialETHCrowdfund:295

        votingPower = _processContribution(contributor, delegate, amount);

        // OK to contribute with zero just to update delegate.
        if (amount == 0) return 0;

        if (tokenId == 0) {
            // Mint contributor a new party card.
            party.mint(contributor, votingPower, delegate);
```

```Solidity
        // ETHCrowdfundBase:263
        
        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
            // Removes funding split from contribution amount in a way that
            // avoids rounding errors for very small contributions <1e4 wei.
            amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
        }

        votingPower = (amount * exchangeRateBps) / 1e4;
```

this down-scaling can incur a precision loss, up to a theoretical `999_999` per contribution, that is not sent back to the user at contribution time nor refunded after up-scaling in case the crowdfund is lost.

Consider refunding the unused tokens when the user contributes to a crowdfund. This wouldn't come at a significant cost in gas, because in the cases where users contribute round figures, an `if` would prevent a useless transfer from happening.

---

# [L-07] OffChainSignatureValidator.isValidSignature misses a check for ecrecover returning address(0)

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/signature-validators/OffChainSignatureValidator.sol#L58

When OffChainSignatureValidator checks a given signature, it uses `ecrecover` to calculate the address that signed the input message:

```Solidity
        // OffChainSignatureValidator.sol:57

        Party party = Party(payable(msg.sender));
        address signer = ecrecover(hash, v, r, s);
        uint96 signerVotingPowerBps = party.getVotingPowerAt(signer, uint40(block.timestamp)) *
            10000;
```

When provided an invalid signature, [that is possible because `v` is not validated, `ecrecover` may return `address(0)`](https://github.com/kadenzipfel/smart-contract-vulnerabilities/blob/master/vulnerabilities/unexpected-ecrecover-null-address.md). 

While this does not represent an immediate threat because `address(0)` cannot hold NFTs in the current Party implementation - so a separate check in `isValidSignature` would fail - in the future this missing check could be part of an attack path.

Consider implementing in-depth defense by:
- adding a check for `v` to be either 27 or 28
- adding a check for `signer != address(0)`

---

# [L-08] Missing check for ETHCrowdfundBase.exchangeRateBps being larger than 10_000

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L166

At ETHCrowdfundBase initialization, the configured `exchangeRateBps` value is validated:

```Solidity
        // Set the exchange rate.
        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
        exchangeRateBps = opts.exchangeRateBps;
```

Being `exchangeRateBps` defined as `uint16`, it is possible that a ETHCrowdfundBase initialized with a value larger than 10.000 (that means 100%) despite the ETHCrowdfundBase assumes 10.000 as maximum value.

Consider adding an extra validation for `exchangeRateBps` being at most 10.000.

---

# [L-09] Zero is an excessively permissive value for OffchainSignatureValidator default signing threshold 

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/signature-validators/OffChainSignatureValidator.sol#L72

When called to validate a signature off-chain, OffchainSignatureValidator checks for a pre-configured threshold in a "party to threshold" map in storage:

```Solidity
        // OffChainSignatureValidator.sol:68

        uint96 thresholdBps = signingThersholdBps[party];

        // Either threshold is 0 or signer votes above threshold
        if (
            thresholdBps == 0 ||
            (signerVotingPowerBps > totalVotingPower &&
                signerVotingPowerBps / totalVotingPower >= thresholdBps)
        ) {
            return IERC1271.isValidSignature.selector;
        }
```

Since map values are zero by default, and a Party can change this default only through a governance action, it is possible that this logic acts more permissively than it needs to, for extended periods of time.

This seems a "permissive by default, strict by option" behavior, whereas one would rather expect it to be "strict by default, permissive by option".

Consider defaulting `thresholdBps` to a reasonably high value i.e. the Party's `passThresholdBps`; an additional magic value can be accommodated within the `uint96` range to allow the Party to opt-in for the "every token holder can sign" behavior through an `ArbitraryCallsProposal`.

---

# [L-10] ETHCrowdfundBase._processContribution can fail due to uint96 overflow

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L270

When processing a contribution within the  `_processContribution` function, `ETHCrowdfundBase` converts the provided ETH amount into votes with this expression:

```Solidity
        // ETHCrowdfundBase:270
        
        // Calculate voting power.
        votingPower = (amount * exchangeRateBps) / 1e4;
```

Since `votingPower` is defined as `uint96` and `exchangeRateBps` can be a number up to `10000`, the multiplication can overflow the `uint96` size implicitly used for evaluating the operation.

Consider using `uint256` to accommodate values inflated by the product during the calculation:

```diff
        // ETHCrowdfundBase:270
        // Calculate voting power.
-       votingPower = (amount * exchangeRateBps) / 1e4;
+       votingPower = ((uint256(amount) * exchangeRateBps) / 1e4).safeCastUint256ToUint96();
```
---

# [I-01] RendererBase does not need to define a virtual contractURI() function

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/renderers/RendererBase.sol#L26

The RendererBase abstract contract defines a `function contractURI() virtual` function. This definition is unnecessary, because RendererBase is an abstract child of `IERC721Renderer`, and this condition is sufficient to enforce at compile-time the presence of an implementation in its children.

Consider removing it.

---

# [I-02] Misleading comment in InitialETHCrowdfund.initialize

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L142

The below comment in the `initialize` function explains how pre-existing balance is contributed:
```Solidity
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
```

This is however incorrect, because only `msg.value` is contributed and pre-existing balance is ignored. 

Consider updating the comment to reflect the contract behavior.

---

# [I-03] InitialContributor and InitialDelegate can be removed from ETHCrowdfundOptions

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L35

The two initialization parameters `initialContributor` and `initialDelegate` that are passed when initializing `ETHCrowdfundOptions` are unused, because the initial contribution is added by the child `InitialETHCrowdfund` contract. 

Consider removing them from `ETHCrowdfundOptions` and cleaning up the code that passes them through from `InitialETHCrowdfundOptions`.

---

# [I-04] Typo in OffChainSignatureValidator

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/signature-validators/OffChainSignatureValidator.sol#L22

Consider renaming the `signingThersholdBps` map to `signingThresholdBps` instead.

