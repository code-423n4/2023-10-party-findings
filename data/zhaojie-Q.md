##  [L-1] Host cannot be restored after it is canceled

In PartyGovernance, Host can pass the address 0 into the `abdicateHost` function to cancel its own host permission, but the host permission cannot be restored after the cancellation.
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L457

##  [L-2] Division overflow occurs when `rageQuit` calculates fee

```solidity
 uint256 fee = (amount * feeBps_) / 1e4; 
```

When the amount * feeBps_ < 1e4 division overflow occurs when 1e4, and feeRecipient receives fee 0.

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L413

##  [L-3] signerVotingPowerBps > totalVotingPower should use require

OffChainSignatureValidator#isValidSignature:

```solidity
       if (
            thresholdBps == 0 ||
            (signerVotingPowerBps > totalVotingPower &&
                signerVotingPowerBps / totalVotingPower >= thresholdBps)
        ) {
            return IERC1271.isValidSignature.selector;
        }
```

advised to change it to:

```solidity
require(signerVotingPowerBps > totalVotingPower);
if (thresholdBps == 0 ||signerVotingPowerBps / totalVotingPower >= thresholdBps) {
   return IERC1271.isValidSignature.selector;
}
```



