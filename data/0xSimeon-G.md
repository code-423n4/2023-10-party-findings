Auditor’s Disclaimer

The gas optimization included in this report has been thoroughly checked. However,  Code reviews and additional testing are strongly advised to minimise any potential risks associated with the refactoring process using recommendations from this gas report.


PS: The reported gas issue below have cross-referenced with the [bot report.](https://github.com/code-423n4/2023-10-party/blob/main/bot-report.md)

[G-00] Early revert `InitialETHCrowdfund::batchContribute()` can save more gas if the length of the `values`, `gateDatas` and `tokenIds` arrays do not match. 

According to the Natspec doc, The `batchContribute` is not expected to revert in the happy path. However, we can potentially hit an `Index out of bounds` even in the happy path if `uint256 numContributions = args.tokenIds.length;` is longer than the other arrays. 
  
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L204

The recommendation: 
 We can revert early (and save +100,000 gas for the caller^) if the array length mismatches to reduce gas wastage at runtime using the check below (add immediately after: [L209](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L207)). 
```solidity 
if (args.values.length != numContributions && args.gateDatas.length != numContributions) {
        revert ArrayLengthMismatch(); 
}
```