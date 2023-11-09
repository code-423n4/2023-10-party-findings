## [L-01] Should validate crowdfunding duration when initializing ETHCrowdfundBase

[contracts/crowdfund/ETHCrowdfundBase.sol#L163](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L163)

Currently, in `ETHCrowdfundBase::_initialize`, there is no validation for `opts.duration`, so this may result in LOST of the crowdfunding if the duration is zero or too short.

I suggest validating `opts.duration` with minimum duration criteria.


## [L-02] `PartyGovernance::_areVotesPassing()` function returns true for vetoed proposals

[contracts/party/PartyGovernance.sol#L1129-L1135](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1129-L1135)

A `voteCount` of a vetoed proposal has a value of `type(uint96).max`. But as there is no consideration of vetoed voting count in `_areVotesPassing()`, the function will almost always return `true` for a vetoed proposal.

So I think there has to be a validation step in the function, which checks the vetoedness like the below:

```solidity
    function _areVotesPassing(
        uint96 voteCount,
        uint96 totalVotingPower,
        uint16 passThresholdBps
    ) private pure returns (bool) {
+       if (voteCount == type(uint96).max) return false; // --> Add this validation
        return (uint256(voteCount) * 1e4) / uint256(totalVotingPower) >= uint256(passThresholdBps);
    }
```


## [L-03] The redundant voting power should be returned in `PartyGovernanceNFT::mint()`

[contracts/party/PartyGovernanceNFT.sol#L166-L202](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L166-L202)

If `totalVotingPower` is greater than zero and the sum of `mintedVotingPower` and `votingPower` to be minted is greater than `totalVotingPower`, there might exist redundant voting power which is not added to `mintedVotingPower`.

```solidity
        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower_) {
            unchecked {
                votingPower_ = totalVotingPower - mintedVotingPower_;
            }
        }
```

In this situation, in my opinion, the redundant voting power `votingPower - votingPower_` should be sent back to the caller as a return value along with a new token Id so that the caller can do further operations like refunding extra contributions.


## [L-04] `OffChainSignatureValidator::isValidSignature()` function reverts with the wrong error when it fails to recover the signer from the signature

[contracts/signature-validators/OffChainSignatureValidator.sol#L58](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/signature-validators/OffChainSignatureValidator.sol#L58)

`ecrecover()` returns `address(0)` when recovering fails due to like hashing wrong EIP-712 format and so on.

In this case, as there is no signature error check, now it reverts with `NotMemberOfParty()`.

I suggest updating the function so that it reverts with the correct error like `revert WrongFormat()`



## [NC-01] Checking length equality is required for member arrays of `BatchContributeArgs` and `BatchContributeForArgs` in `InitialETHCrowdfund::batchContribute()` and `InitialETHCrowdfund::batchContributeFor()`

Missing equality check of member arrays of `args` variable may cause out-of-range exception.

batchContribute(): [contracts/crowdfund/InitialETHCrowdfund.sol#L204-L225](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L204-L225)
batchConttributeFor(): [contracts/crowdfund/InitialETHCrowdfund.sol#L255-L273](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L255-L273)


## [NC-02] Zero address(`address(0)`) check of `contributor` is missing in `InitialETHCrowdfund::_contribute()`

[contracts/crowdfund/InitialETHCrowdfund.sol#L282-L285](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L282-L285)


## [NC-03] Checking length equality is missing between `withdrawTokens` and `minWithdrawAmounts` in `PartyGovernanceNFT::rageQuit()`

Missing length equality check between `withdrawTokens` and `minWithdrawAmounts` variable may cause out-of-range exception while running `rageQuit()`

[contracts/party/PartyGovernanceNFT.sol#L350-L366](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L350-L366)
