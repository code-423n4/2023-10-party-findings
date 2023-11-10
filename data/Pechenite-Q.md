## [Low-01] Incorrect `minTotalContribution` and `minContribution` Interaction

### Impact

The issue impacts the functionality and finalization of the `ETHCrowdfundBase` contract.
Specifically, it arises from an incorrect interaction between the `minTotalContribution`, `maxTotalContribution`, and `minContribution` parameters. When the difference between `minTotalContribution` and `maxTotalContribution` is smaller than `minContribution`, the `ETHCrowdfundBase` contract will end up in situation where the crowdfund cannot be finalized, even if there are enough contributors willing to participate.

### Explanation

In the `ETHCrowdfundBase` contract, two critical parameters govern the crowdfund's behavior:

1. `minTotalContribution`: This parameter specifies the minimum total contribution required for the crowdfund to be considered successful.

2. `minContribution`: This parameter sets the minimum contribution amount required from individual contributors to participate in the crowdfund.

The issue arises when the difference between `minTotalContribution` and `maxTotalContribution` (the maximum allowed total contribution) is less than `minContribution`. This scenario creates a problematic situation for the crowdfund:

1. The crowdfund has a goal defined by `minTotalContribution` that must be reached for it to be considered successful (to can be finalized).

2. However, due to the narrow gap between `minTotalContribution` and `maxTotalContribution`, it becomes challenging for contributors to reach the goal.

3. If contributors collectively contribute enough to meet or exceed the `minTotalContribution`, the condition for finalizing the crowdfund is met.

4. Nevertheless, because the difference between `minTotalContribution` and `maxTotalContribution` is smaller than `minContribution`, the `_processContribution()` function may prevent contributors from participating, causing the crowdfund to remain in an incomplete state.

This situation can result in a scenario where contributors are willing to contribute enough to meet the crowdfund's minimum goal (`minTotalContribution`), but they are unable to do so due to the restrictive `minContribution` check. As a result, the crowdfund may never be successfully finalized.

```js
    function _processContribution(
        address payable contributor,
        address delegate,
        uint96 amount
    ) internal returns (uint96 votingPower) {
        
        /// ...code...

        // Check that the contribution amount is at or below the maximum.
        uint96 maxContribution_ = maxContribution;
        if (amount > maxContribution_) {
            revert AboveMaximumContributionsError(amount, maxContribution_);
        }

        uint96 newTotalContributions = totalContributions + amount;
        uint96 maxTotalContributions_ = maxTotalContributions;
        if (newTotalContributions >= maxTotalContributions_) {
            totalContributions = maxTotalContributions_;

            // Finalize the crowdfund.
            // This occurs before refunding excess contribution to act as a
            // reentrancy guard.
            _finalize(maxTotalContributions_);

            // Refund excess contribution.
            uint96 refundAmount = newTotalContributions - maxTotalContributions;
            if (refundAmount > 0) {
                amount -= refundAmount;
                payable(msg.sender).transferEth(refundAmount);
            }
        } else {
            totalContributions = newTotalContributions;
        }

        // Check that the contribution amount is at or above the minimum. This
        // is done after `amount` is potentially reduced if refunding excess
        // contribution. There is a case where this prevents a crowdfunds from
        // reaching `maxTotalContributions` if the `minContribution` is greater
        // than the difference between `maxTotalContributions` and the current
        // `totalContributions`. In this scenario users will have to wait until
        // the crowdfund expires or a host finalizes after
        // `minTotalContribution` has been reached by calling `finalize()`.
        uint96 minContribution_ = minContribution;
        if (amount < minContribution_) {
            revert BelowMinimumContributionsError(amount, minContribution_);
        }

        /// ...code...

    }
```

### Recommended Mitigation Steps
Implement check in Crowdfund Initialization that ensure the difference between `minTotalContribution` and `maxTotalContribution` is greater than `minContribution`.

```diff
    function _initialize(ETHCrowdfundOptions memory opts) internal {
        // Set the minimum and maximum contribution amounts.
        if (opts.minContribution > opts.maxContribution) {
            revert MinGreaterThanMaxError(
                opts.minContribution,
                opts.maxContribution
            );
        }
        minContribution = opts.minContribution;
        maxContribution = opts.maxContribution;
        // Set the min total contributions.
        if (opts.minTotalContributions > opts.maxTotalContributions) {
            revert MinGreaterThanMaxError(
                opts.minTotalContributions,
                opts.maxTotalContributions
            );
        }
        minTotalContributions = opts.minTotalContributions;
        // Set the max total contributions.
        if (opts.maxTotalContributions == 0) {
            // Prevent this because when `maxTotalContributions` is 0 the
            // crowdfund is invalid in `getCrowdfundLifecycle()` meaning it has
            // never been initialized.
            revert MaxTotalContributionsCannotBeZeroError(
                opts.maxTotalContributions
            );
        }
        maxTotalContributions = opts.maxTotalContributions;

+       if (maxTotalContributions - minTotalContributions < minContribution) {
+           revert();
+       }

        // Set the party crowdfund is for.
        party = opts.party;
        // Set the crowdfund start and end timestamps.
        expiry = uint40(block.timestamp + opts.duration);
        // Set the exchange rate.
        if (opts.exchangeRateBps == 0)
            revert InvalidExchangeRateError(opts.exchangeRateBps);
        exchangeRateBps = opts.exchangeRateBps;
        // Set the funding split and its recipient.
        fundingSplitBps = opts.fundingSplitBps;
        fundingSplitRecipient = opts.fundingSplitRecipient;
        // Set whether to disable contributing for existing card.
        disableContributingForExistingCard = opts
            .disableContributingForExistingCard;
    }
```

---

## [Low-02] `delegationsByVoter[voter]` is not deleted after the party member tokens are burned

### Coded Proof of Concept
```js
    function testsForReports() public {
        // 1. Create Party
        (
            Party party,
            IERC721[] memory preciousTokens,
            uint256[] memory preciousTokenIds
        ) = partyAdmin.createParty(
                partyImpl,
                PartyAdmin.PartyCreationMinimalOptions({
                    host1: address(danny),
                    host2: address(this),
                    passThresholdBps: 5000,
                    totalVotingPower: 60,
                    preciousTokenAddress: address(toadz),
                    preciousTokenId: 1,
                    rageQuitTimestamp: 0,
                    feeBps: 0,
                    feeRecipient: payable(0)
                })
            );
        DummySimpleProposalEngineImpl engInstance = DummySimpleProposalEngineImpl(
                address(party)
            );

        // 2. Mint Governance NFTs for `john` and `danny`
        partyAdmin.mintGovNft(party, address(john), 20, address(john));
        assertEq(party.votingPowerByTokenId(1), 20);
        assertEq(party.ownerOf(1), address(john));

        partyAdmin.mintGovNft(party, address(danny), 20, address(danny));
        assertEq(party.votingPowerByTokenId(2), 20); //@note the governance token id of `danny` is `2`
        assertEq(party.ownerOf(2), address(danny));

        // 3. Danny delegate his voting power to `steve`
        vm.prank(address(danny));
        party.delegateVotingPower(address(steve));

        // 4. `john` Generate Proposal
        PartyGovernance.Proposal memory p1 = PartyGovernance.Proposal({
            maxExecutableTime: 9999999999,
            proposalData: abi.encodePacked([0]),
            cancelDelay: uint40(1 days)
        });
        john.makeProposal(party, p1, 2);

        // 5. Party Admin burn NFTs of `danny`
        vm.prank(address(partyAdmin));
        party.burn(2);

        assertEq(party.delegationsByVoter(address(danny)), address(steve)); // steve is still in `delegationsByVoter` mapping
    }
```

---

## [Low-03] In `PartyGovernanceNFT.sol#increaseVotingPower()` and `PartyGovernanceNFT.sol#decreaseVotingPower()` add check if the tokenId is actually minted to the specified user token. this can lead to more serious bugs.

---

## [Low-04] `emergencyExecuteDisabled` cannot be set to `false` after `disableEmergencyExecute()`. Add functionality that allow `emergencyExecuteDisabled` to be set to `false`, no only to `true`

---

## [Low-05] Outdated comments in the contracts NatSpecs
### Example: `PartyGovernanceNFT.sol#burn()`
 **In the contract NatSpec:**
    /// @notice Burn governance NFTs and remove their voting power. Can only
    ///         be called by an authority before the party has started.
    /// @param tokenIds The IDs of the governance NFTs to burn. 

 **In the [contest page](https://code4rena.com/contests/2023-10-party-protocol#top:~:text=Update%20PartyGovernanceNFT.burn()%20to%20allow%20authorities%20to%20burn%20any%20Party%20card%20even%20after%20governance%20has%20started)**
    Update PartyGovernanceNFT.burn() to allow authorities to burn any Party card even after governance has started

---

## [Low-06] Verification of `rageQuitTimestamp` in `PartyGovernanceNFT._initialize()`
### GitHub Links:
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L81-L110

### Explanation:
The functionality provided by the `rageQuit()` feature is of great importance to Party participants as it serves as a safeguard allowing them to make an emergency withdrawal of their assets. In light of this, it would be a prudent and sensible default strategy to set the initial value of rageQuitTimestamp to a future date. This would provide Party members with the assurance that they can utilize the rageQuit function during the early phases of the Party if necessary.

---

## [Low-07] Restriction on Host's Authority to Deactivate emergencyExecute
### GitHub Links:
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L855-L858

The `emergencyExecute` feature serves as a safeguard, enabling the PartyDAO to execute arbitrary functions on behalf of the Party contract and manage assets when the Party contract encounters issues. This functionality proves invaluable when assets are stuck, offering Party members an avenue to recover their locked assets. In light of this, permitting any Host to deactivate the `emergencyExecute()` capability appears to bestow an excessive degree of authority upon the role and necessitates a high level of trust in the Host role. Additionally, it's important to note that deactivating `emergencyExecute` is an irreversible and permanent action. It may be worthwhile to contemplate granting activeMembers the ability to both enable and disable `emergencyExecute`, thereby promoting a more balanced control mechanism.

---

## [Low-08] Return value of 0 from `ecrecover` not checked (Missing Validation)

### GitHub Links:
https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L58

```js
    address signer = ecrecover(hash, v, r, s);
```

### Reference
https://scsfg.io/hackers/signature-attacks/#missing-validation

---
---

## [NC-01] Outdated documentation
### Examples:
- for modifier of `propose()`
- for modifier of `veto()`

---

## [NC-02] Add check if `opts.duration` is zero, and if it is then revert in `ETHCrowdfundBase.sol#_initialize()`

---

## [NC-03] `PartyGovernance.sol#supportsInterface()` `interfaceId == 0x49064906` -> `interfaceId == bytes4(0x49064906)`

---

## [NC-04] `PartyGovernanceNFT.sol#function burn(uint256 tokenId)` is useless. Can be removed. Additionally, this function is never used.

---

## [NC-05] `PartyGovernanceNFT.sol#addAuthority()` - add check if the `authority` is EOA, and if it is then revert

---

## [NC-06] Party can be in state without authorities. Implement some logic that prevent this.

---
