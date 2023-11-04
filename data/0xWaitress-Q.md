[N-1] isValidSignature in OffChainSignatureValidator has an unnecessary check

since `signerVotingPowerBps` is user's votingPower * 10000, the check 
`signerVotingPowerBps / totalVotingPower >= thresholdBps` is sufficient, 
`signerVotingPowerBps > totalVotingPower` is redundant.


```solidity
        uint96 signerVotingPowerBps = party.getVotingPowerAt(signer, uint40(block.timestamp)) *
            10000;

        if (signerVotingPowerBps == 0 && party.balanceOf(signer) == 0) {
            // Must own a party card or be delegatated voting power
            revert NotMemberOfParty();
        }

        uint96 totalVotingPower = party.getGovernanceValues().totalVotingPower;
        uint96 thresholdBps = signingThersholdBps[party];

        // Either threshold is 0 or signer votes above threshold
        if (
            thresholdBps == 0 ||
  @>unnecessary          (signerVotingPowerBps > totalVotingPower &&
                signerVotingPowerBps / totalVotingPower >= thresholdBps)
        ) {
            return IERC1271.isValidSignature.selector;
        }
```