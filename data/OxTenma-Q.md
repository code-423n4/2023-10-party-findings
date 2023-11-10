## Summary

### Low Risk Issues
|Tag |Issue|Contexts|
|-|:-|:-:|
|LOW-1|`OffChainSignatureValidator::isValidSignature` doesn't follow 1271 standard|1| 
|LOW-2|Use `require` instead of `assert` in `PartyGovernance::distribute`| 1 | 
|LOW-3| `SafeTransferLib` does not ensure that the token contract exists| 2 |

Total: 4 contexts over 3 issues

### LOW-1: `OffChainSignatureValidator::isValidSignature` doesn't follow 1271 standard
The function `OffChainSignatureValidator::isValidSignature` doesn't follow 1271 EIP Standard. ecrecover is susceptible to signature malleability.

Context: [OffChainSignatureValidator::isValidSignature](https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L28)
```solidity
        address signer = ecrecover(hash, v, r, s);
```


### LOW-2: Use `require` instead of `assert` in `PartyGovernance::distribute`
The function `PartyGovernance::distribute` uses assert instead of require. `assert` doesn't return the gas like `require/revert`.

Context: [PartyGovernance::distribute](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L533)
```solidity
       assert(tokenType == ITokenDistributor.TokenType.Erc20);
```

## LOW-3: `SafeTransferLib` does not ensure that the token contract exists
Solday's `SafeTransferLib` implementation doesn't check if the token is a contract or not. 

Context: PartyGovernanceNFT::safeTransferFrom( [#L461](https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/party/PartyGovernanceNFT.sol#L461), [#L473](https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/party/PartyGovernanceNFT.sol#L473) )
```solidity
@L#461>    super.safeTransferFrom(owner, to, tokenId);

@L#473>     super.safeTransferFrom(owner, to, tokenId, data);
```