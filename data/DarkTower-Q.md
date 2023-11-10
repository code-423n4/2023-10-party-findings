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
 One solution would be to add the checks or use OZ library
```javascript
if (uint256(s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) {
      revert("SignatureValidator#recoverSigner: invalid signature 's' value");
    }

    if (v != 27 && v != 28) {
      revert("SignatureValidator#recoverSigner: invalid signature 'v' value");
    }
```

The function `OffChainSignatureValidator::isValidSignature` uses the non conventionnal way
`v := mload(add(signature, 0x41))` + implicit conversion to bytes 1 (uint8) to retrieve the v parameter from the signature. This works but for better readability and standard it is better the follow the standard way of retrieving the v element from the signature :
`v := byte(0, mload(add(signature, 0x60)))`

### LOW-2: Use `require` instead of `assert` in `PartyGovernance::distribute`
The function `PartyGovernance::distribute` uses assert instead of require. Utilizing `assert` in Solidity, especially versions prior to 0.8.0, can lead to unintended consequences regarding gas usage. In versions before 0.8.0, a failed `assert` consumes all remaining gas in a transaction. From 0.8.0 onwards, while it doesn’t consume all remaining gas, it does consume a specific amount, designed to discourage its use. The Solidity documentation underscores that a failed `assert` typically indicates a contract bug, as it should not be triggered during normal operation or through external inputs.

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