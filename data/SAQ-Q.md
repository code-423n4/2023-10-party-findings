
## Summary

### Low Risk Issues

no | Issue |Instances|
|-|:-|:-:|
| [L-01] | Loss of precision | 2 | 

## Low Risk Issues

### [L-01] Loss of precision

Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator.

```solidity
file: /contracts/party/PartyGovernance.sol
  
1134        return (uint256(voteCount) * 1e4) / uint256(totalVotingPower) >= uint256(passThresholdBps);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1134


```solidity
file: /contracts/party/PartyGovernanceNFT.sol

413                uint256 fee = (amount * feeBps_) / 1e4;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L413
