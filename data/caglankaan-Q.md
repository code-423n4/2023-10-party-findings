
---

## Low Risk Issues

---



### [L-01] Missing checks for `address(0)` when setting `address` state variables
### Severity
* Impact: Medium
* Likelihood: Low
### Description

In Solidity, the Ethereum address `0x0000000000000000000000000000000000000000` is known as the "zero address". This address has significance because it's the default value for uninitialized address variables and is often used to represent an invalid or non-existent address. The "Missing zero address control" issue arises when a Solidity smart contract does not properly check or prevent interactions with the zero address, leading to unintended behavior.
For instance, a contract might allow tokens to be sent to the zero address without any checks, which essentially burns those tokens as they become irretrievable. While sometimes this is intentional, without proper control or checks, accidental transfers could occur.    
        

### Number Of Instances Found
19

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

39:        _GLOBALS = globals;	// @audit-issue
```
*GitHub*: [39](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L39-L39)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

173:        _GLOBALS = globals;	// @audit-issue
```
*GitHub*: [173](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L173-L173)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

135:        _GLOBALS = globals;	// @audit-issue
```
*GitHub*: [135](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L135-L135)
```solidity
Path: ./contracts/crowdfund/ContributionRouter.sol

29:        OWNER = owner;	// @audit-issue
```
*GitHub*: [29](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L29-L29)
```solidity
Path: ./contracts/crowdfund/AtomicManualParty.sol

36:        PARTY_FACTORY = partyFactory;	// @audit-issue
```
*GitHub*: [36](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L36-L36)
```solidity
Path: ./contracts/globals/Globals.sol

47:        multiSig = multiSig_;	// @audit-issue

52:        pendingMultiSig = newMultiSig;	// @audit-issue
```
*GitHub*: [47](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L47-L47), [52](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L52-L52)
```solidity
Path: ./contracts/party/PartyFactory.sol

27:        _GLOBALS = globals;	// @audit-issue
```
*GitHub*: [27](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyFactory.sol#L27-L27)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

77:        _GLOBALS = globals;	// @audit-issue
```
*GitHub*: [77](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L77-L77)
```solidity
Path: ./contracts/party/PartyGovernance.sol

266:        _GLOBALS = globals;	// @audit-issue
```
*GitHub*: [266](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L266-L266)
```solidity
Path: ./contracts/market-wrapper/NounsMarketWrapper.sol

24:        market = INounsAuctionHouse(_nounsAuctionHouse);	// @audit-issue
```
*GitHub*: [24](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/NounsMarketWrapper.sol#L24-L24)
```solidity
Path: ./contracts/market-wrapper/KoansMarketWrapper.sol

25:        market = IKoansAuctionHouse(_koansAuctionHouse);	// @audit-issue
```
*GitHub*: [25](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/KoansMarketWrapper.sol#L25-L25)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

117:        _GLOBALS = globals;	// @audit-issue
```
*GitHub*: [117](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L117-L117)
```solidity
Path: ./contracts/proposals/ArbitraryCallsProposal.sol

49:        _ZORA = zora;	// @audit-issue
```
*GitHub*: [49](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L49-L49)
```solidity
Path: ./contracts/proposals/FractionalizeProposal.sol

32:        VAULT_FACTORY = vaultFactory;	// @audit-issue
```
*GitHub*: [32](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/FractionalizeProposal.sol#L32-L32)
```solidity
Path: ./contracts/gatekeepers/AllowListGateKeeper.sol

17:        CONTRIBUTION_ROUTER = contributionRouter;	// @audit-issue
```
*GitHub*: [17](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/gatekeepers/AllowListGateKeeper.sol#L17-L17)
```solidity
Path: ./contracts/gatekeepers/TokenGateKeeper.sol

24:        CONTRIBUTION_ROUTER = contributionRouter;	// @audit-issue
```
*GitHub*: [24](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/gatekeepers/TokenGateKeeper.sol#L24-L24)
```solidity
Path: ./contracts/operators/ERC20SwapOperator.sol

83:        _GLOBALS = globals;	// @audit-issue
```
*GitHub*: [83](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L83-L83)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

99:        GLOBALS = globals;	// @audit-issue
```
*GitHub*: [99](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L99-L99)
</details>



### [L-02] Floating Pragma
### Severity
* Impact: Low
* Likelihood: Medium
### Description

A "floating pragma" in Solidity refers to the practice of using a pragma statement that does not specify a fixed compiler version but instead allows the contract to be compiled with any compatible compiler version. This issue arises when pragma statements like `pragma solidity ^0.8.0;` are used without a specific version number, allowing the contract to be compiled with the latest available compiler version. This can lead to various compatibility and stability issues.


### Impact

1. **Version Compatibility:** Using a floating pragma makes the contract susceptible to potential breaking changes or unexpected behavior introduced in newer compiler versions. Contracts that rely on specific compiler features or behaviors may break when compiled with a different version.

2. **Security Risks:** Newer compiler versions may include security updates and bug fixes. By not specifying a fixed compiler version, the contract may miss out on important security improvements, exposing it to vulnerabilities that have already been patched in later versions.

3. **Contract Reliability:** Contracts with floating pragmas are less reliable and predictable. They may behave differently when deployed on different compiler versions, making it challenging to ensure consistent contract behavior.

4. **Maintenance Complexity:** Contracts with floating pragmas may require frequent updates and modifications to remain compatible with the latest compiler versions. This increases maintenance complexity and the risk of introducing errors during updates.

5. **Interoperability Issues:** Contracts compiled with different compiler versions may have compatibility issues when interacting with each other or with external services. This can hinder the interoperability of the contract within the Ethereum ecosystem.

6. **Auditing Challenges:** Auditors and security experts may find it more challenging to assess the security of contracts with floating pragmas, as they cannot rely on a specific compiler version to understand the contract's behavior and potential vulnerabilities.

To mitigate these risks, it is recommended to use a fixed pragma statement that specifies a known, well-tested compiler version. This helps ensure the stability, security, and predictability of the smart contract throughout its lifecycle.


### Number Of Instances Found
18

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/utils/IERC4906.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/IERC4906.sol#L2-L2)
```solidity
Path: ./contracts/utils/vendor/Base64.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Base64.sol#L2-L2)
```solidity
Path: ./contracts/utils/vendor/Strings.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L2-L2)
```solidity
Path: ./contracts/proposals/vendor/IOpenseaConduitController.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/vendor/IOpenseaConduitController.sol#L2-L2)
```solidity
Path: ./contracts/proposals/vendor/FractionalV1.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/vendor/FractionalV1.sol#L2-L2)
```solidity
Path: ./contracts/proposals/vendor/IOpenseaExchange.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/vendor/IOpenseaExchange.sol#L2-L2)
```solidity
Path: ./contracts/tokens/IERC1155.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/tokens/IERC1155.sol#L2-L2)
```solidity
Path: ./contracts/tokens/IERC20.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/tokens/IERC20.sol#L2-L2)
```solidity
Path: ./contracts/tokens/IERC721Receiver.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/tokens/IERC721Receiver.sol#L2-L2)
```solidity
Path: ./contracts/tokens/IERC721.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/tokens/IERC721.sol#L2-L2)
```solidity
Path: ./contracts/tokens/ERC721Receiver.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/tokens/ERC721Receiver.sol#L2-L2)
```solidity
Path: ./contracts/tokens/ERC1155Receiver.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/tokens/ERC1155Receiver.sol#L2-L2)
```solidity
Path: ./contracts/vendor/markets/INounsAuctionHouse.sol

18:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [18](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/markets/INounsAuctionHouse.sol#L18-L18)
```solidity
Path: ./contracts/vendor/markets/IZoraAuctionHouse.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/markets/IZoraAuctionHouse.sol#L2-L2)
```solidity
Path: ./contracts/vendor/markets/IKoansAuctionHouse.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/markets/IKoansAuctionHouse.sol#L2-L2)
```solidity
Path: ./contracts/vendor/markets/IReserveAuctionCoreEth.sol

2:pragma solidity ^0.8.10;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/markets/IReserveAuctionCoreEth.sol#L2-L2)
```solidity
Path: ./contracts/vendor/solmate/ERC1155.sol

5:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [5](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L5-L5)
```solidity
Path: ./deploy/LibDeployConstants.sol

2:pragma solidity ^0.8;	// @audit-issue
```
*GitHub*: [2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L2-L2)
</details>



### [L-03] Use Of `transfer` or `send` Instead Of `call` To Send Native Assets
### Severity
* Impact: Low
* Likelihood: Medium
### Description

The use of `transfer()` in the contracts may have unintended outcomes on the native asset being sent to the receiver. The transaction will fail when:

- The receiver address is a smart contract that does not implement a payable function.
- The receiver address is a smart contract that implements a payable fallback function which uses more than 2300 gas units.
- The receiver address is a smart contract that implements a payable fallback function that needs less than 2300 gas units but is called through proxy, raising the call's gas usage above 2300.
- Additionally, using a gas value higher than 2300 might be mandatory for some multi-signature wallets.




### Number Of Instances Found
1

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/InitialETHCrowdfund.sol

224:        if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);	// @audit-issue
```
*GitHub*: [224](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L224-L224)
</details>



### [L-04] Avoid Double `casting`
### Severity
* Impact: Low
* Likelihood: Low
### Description

Consider refactoring the following code, as double casting may introduce unexpected truncations and/or rounding issues.

Furthermore, double type casting can make the code less readable and harder to maintain, increasing the likelihood of errors and misunderstandings during development and debugging.


### Number Of Instances Found
1

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/utils/LibSafeCast.sol

44:    function safeCastUint256ToInt128(uint256 x) internal pure returns (int128) {
45:        if (x > uint256(uint128(type(int128).max))) {	// @audit-issue
46:            revert Uint256ToInt128CastOutOfRangeError(x);
47:        }
48:        return int128(uint128(x));
49:    }
```
*GitHub*: [45](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L45-L45)
</details>



### [L-05] Use of `ecrecover` is susceptible to signature malleability
### Severity
* Impact: Medium
* Likelihood: Low
### Description

The built-in EVM precompile `ecrecover` is susceptible to signature malleability, which could lead to [replay attacks](https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57).

Consider using OpenZeppelin’s [ECDSA library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol#L125-L128) instead of the built-in function.

### Number Of Instances Found
2

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/vendor/solmate/ERC20.sol

120:            address recoveredAddress = ecrecover(	// @audit-issue
```
*GitHub*: [120](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC20.sol#L120-L120)
```solidity
Path: ./contracts/signature-validators/OffChainSignatureValidator.sol

58:        address signer = ecrecover(hash, v, r, s);	// @audit-issue
```
*GitHub*: [58](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L58-L58)
</details>



### [L-06] Empty `receive()`/`payable fallback()` function does not authorize requests
### Severity
* Impact: Low
* Likelihood: Low
### Description

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. `require(msg.sender == address(weth))`). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.

### Number Of Instances Found
4

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

162:    receive() external payable {}
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L-L)
```solidity
Path: ./contracts/party/Party.sol

53:    receive() external payable {}
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/Party.sol#L-L)
```solidity
Path: ./contracts/operators/ERC20SwapOperator.sol

211:    receive() external payable {}
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L-L)
```solidity
Path: ./contracts/operators/CollectionBatchBuyOperator.sol

243:    receive() external payable {}
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/CollectionBatchBuyOperator.sol#L-L)
</details>



### [L-07] Consider using OpenZeppelin’s `SafeCast` library to prevent unexpected overflows when casting from various type int/uint values
### Severity
* Impact: Low
* Likelihood: Low
### Description


### Number Of Instances Found
61

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

127:        return _owners[uint256(uint160(owner))] != address(0);	// @audit-issue

132:        tokenId = uint256(uint160(owner));	// @audit-issue

142:        uint256 tokenId = uint256(uint160(owner));	// @audit-issue
```
*GitHub*: [127](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L127-L127), [132](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L132-L132), [142](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L142-L142)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

684:        emit MetadataUpdate(uint256(uint160(contributor)));	// @audit-issue
```
*GitHub*: [684](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L684-L684)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

129:        expiry = uint40(opts.duration + block.timestamp);	// @audit-issue
```
*GitHub*: [129](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L129-L129)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

163:        expiry = uint40(block.timestamp + opts.duration);	// @audit-issue
```
*GitHub*: [163](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L163-L163)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfundBase.sol

78:        expiry = uint40(opts.duration + block.timestamp);	// @audit-issue

159:            expiry = uint40(block.timestamp);	// @audit-issue
```
*GitHub*: [78](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L78-L78), [159](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L159-L159)
```solidity
Path: ./contracts/globals/Globals.sol

68:        return uint256(_wordValues[key]);	// @audit-issue

72:        uint256 value = uint256(_wordValues[key]);	// @audit-issue

80:        return address(uint160(uint256(_wordValues[key])));	// @audit-issue

84:        return Implementation(address(uint160(uint256(_wordValues[key]))));	// @audit-issue

96:        return _includedWordValues[key][bytes32(uint256(uint160(value)))];	// @audit-issue

110:        emit ValueSet(key, _wordValues[key], value ? bytes32(uint256(1)) : bytes32(0));	// @audit-issue

111:        _wordValues[key] = value ? bytes32(uint256(1)) : bytes32(0);	// @audit-issue

115:        emit ValueSet(key, _wordValues[key], bytes32(uint256(uint160(value))));	// @audit-issue

116:        _wordValues[key] = bytes32(uint256(uint160(value)));	// @audit-issue

132:            bytes32(uint256(uint160(value))),	// @audit-issue

133:            _includedWordValues[key][bytes32(uint256(uint160(value)))],	// @audit-issue

136:        _includedWordValues[key][bytes32(uint256(uint160(value)))] = isIncluded;	// @audit-issue
```
*GitHub*: [68](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L68-L68), [72](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L72-L72), [80](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L80-L80), [84](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L84-L84), [96](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L96-L96), [110](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L110-L110), [111](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L111-L111), [115](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L115-L115), [116](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L116-L116), [132](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L132-L132), [133](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L133-L133), [136](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L136-L136)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

372:        lastRageQuitTimestamp = uint40(block.timestamp);	// @audit-issue
```
*GitHub*: [372](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L372-L372)
```solidity
Path: ./contracts/party/PartyGovernance.sol

295:        numHosts = uint8(govOpts.hosts.length);	// @audit-issue

565:                proposedTime: uint40(block.timestamp),	// @audit-issue

651:            info.values.passedTime = uint40(block.timestamp);	// @audit-issue

734:                    uint40(block.timestamp)	// @audit-issue

737:            proposalState.values.executedTime = uint40(block.timestamp);	// @audit-issue

745:        proposalState.values.completedTime = uint40(block.timestamp);	// @audit-issue

813:                    uint40(block.timestamp),	// @audit-issue

814:                    uint40(cancelTime)	// @audit-issue

820:        proposalState.values.completedTime = uint40(block.timestamp | UINT40_HIGH_BIT);	// @audit-issue

954:            timestamp: uint40(block.timestamp),	// @audit-issue

988:                timestamp: uint40(block.timestamp),	// @audit-issue

1011:                timestamp: uint40(block.timestamp),	// @audit-issue

1086:        uint40 t = uint40(block.timestamp);	// @audit-issue

1134:        return (uint256(voteCount) * 1e4) / uint256(totalVotingPower) >= uint256(passThresholdBps);	// @audit-issue
```
*GitHub*: [295](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L295-L295), [565](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L565-L565), [651](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L651-L651), [734](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L734-L734), [737](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L737-L737), [745](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L745-L745), [813](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L813-L813), [814](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L814-L814), [820](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L820-L820), [954](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L954-L954), [988](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L988-L988), [1011](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1011-L1011), [1086](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1086-L1086), [1134](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1134-L1134)
```solidity
Path: ./contracts/utils/LibSafeCast.sol

13:        if (v > uint256(type(uint96).max)) {	// @audit-issue

16:        return uint96(v);	// @audit-issue

20:        if (v > uint256(type(uint128).max)) {	// @audit-issue

23:        return uint128(v);	// @audit-issue

27:        if (v > uint256(uint192(type(int192).max))) {	// @audit-issue

30:        return int192(uint192(v));	// @audit-issue

34:        return int192(uint192(v));	// @audit-issue

38:        if (i192 < 0 || i192 > int192(uint192(type(uint96).max))) {	// @audit-issue

41:        return uint96(uint192(i192));	// @audit-issue

45:        if (x > uint256(uint128(type(int128).max))) {	// @audit-issue

48:        return int128(uint128(x));	// @audit-issue

52:        if (x > uint256(type(uint40).max)) {	// @audit-issue

55:        return uint40(x);	// @audit-issue
```
*GitHub*: [13](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L13-L13), [16](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L16-L16), [20](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L20-L20), [23](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L23-L23), [27](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L27-L27), [30](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L30-L30), [34](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L34-L34), [38](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L38-L38), [41](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L41-L41), [45](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L45-L45), [48](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L48-L48), [52](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L52-L52), [55](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L55-L55)
```solidity
Path: ./contracts/utils/vendor/Strings.sol

28:            buffer[digits] = bytes1(uint8(48 + uint256(value % 10)));	// @audit-issue

69:        return toHexString(uint256(uint160(addr)), _ADDRESS_LENGTH);	// @audit-issue
```
*GitHub*: [28](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L28-L28), [69](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L69-L69)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

159:                uint40 zoraTimeout = uint40(	// @audit-issue

162:                uint40 zoraDuration = uint40(	// @audit-issue

224:                uint40 minDuration = uint40(	// @audit-issue

227:                uint40 maxDuration = uint40(	// @audit-issue

236:            uint256 expiry = block.timestamp + uint256(data.duration);	// @audit-issue

287:        orderParams.salt = uint256(bytes32(data.domainHashPrefix));	// @audit-issue
```
*GitHub*: [159](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L159-L159), [162](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L162-L162), [224](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L224-L224), [227](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L227-L227), [236](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L236-L236), [287](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L287-L287)
```solidity
Path: ./contracts/gatekeepers/AllowListGateKeeper.sol

36:        return MerkleProof.verify(proof, merkleRoots[uint96(id)], leaf);	// @audit-issue
```
*GitHub*: [36](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/gatekeepers/AllowListGateKeeper.sol#L36-L36)
```solidity
Path: ./contracts/gatekeepers/TokenGateKeeper.sol

46:        TokenGate memory _gate = gateInfo[uint96(id)];	// @audit-issue
```
*GitHub*: [46](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/gatekeepers/TokenGateKeeper.sol#L46-L46)
```solidity
Path: ./contracts/signature-validators/OffChainSignatureValidator.sol

59:        uint96 signerVotingPowerBps = party.getVotingPowerAt(signer, uint40(block.timestamp)) *	// @audit-issue
```
*GitHub*: [59](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L59-L59)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

401:            return bytes32(uint256(uint160(NATIVE_TOKEN_ADDRESS)));	// @audit-issue

404:        return bytes32(uint256(uint160(token)));	// @audit-issue
```
*GitHub*: [401](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L401-L401), [404](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L404-L404)
</details>



### [L-08] Numbers downcast to `addresses` may result in collisions
### Severity
* Impact: Low
* Likelihood: Low
### Description

If a number is downcast to an `address` the upper bytes are truncated, which may mean that more than one value will map to the `address`


### Number Of Instances Found
2

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/globals/Globals.sol

80:        return address(uint160(uint256(_wordValues[key])));	// @audit-issue

84:        return Implementation(address(uint160(uint256(_wordValues[key]))));	// @audit-issue
```
*GitHub*: [80](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L80-L80), [84](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L84-L84)
</details>



---

## Non-Critical Issues

---



### [N-01] `if`-statement can be converted to a ternary
### Severity
* Impact: Low
* Likelihood: Low
### Description

The code can be made more compact while also increasing readability by converting the following `if`-statements to ternaries (e.g. `foo += (x > y) ? a : b`)

### Number Of Instances Found
5

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

202:        if (msg.sender == contributor || oldDelegate == address(0)) {
203:            // Update delegate.
204:            delegationsByContributor[contributor] = delegate;
205:        } else {
206:            // Prevent changing another's delegate if already delegated.
207:            delegate = oldDelegate;
208:        }
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L-L)
```solidity
Path: ./contracts/party/PartyGovernance.sol

435:            if (snaps[mid].timestamp > timestamp) {
436:                // Entry is too recent.
437:                high = mid;
438:            } else {
439:                // Entry is older. This is our best guess for now.
440:                low = mid + 1;
441:            }
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L-L)
```solidity
Path: ./contracts/market-wrapper/NounsMarketWrapper.sol

112:        if (market.paused()) {
113:            market.settleAuction();
114:        } else {
115:            market.settleCurrentAndCreateNewAuction();
116:        }
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/NounsMarketWrapper.sol#L-L)
```solidity
Path: ./contracts/market-wrapper/KoansMarketWrapper.sol

113:        if (market.paused()) {
114:            market.settleAuction();
115:        } else {
116:            market.settleCurrentAndCreateNewAuction();
117:        }
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/KoansMarketWrapper.sol#L-L)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

362:        if (tokenType == TokenType.ERC721) {
363:            IERC721(token).approve(conduit, tokenId);
364:        } else {
365:            IERC1155(token).setApprovalForAll(conduit, true);
366:        }
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L-L)
</details>



### [N-02] Non-`external`/`public` variable and function names should begin with an underscore
### Severity
* Impact: Low
* Likelihood: Low
### Description

According to the Solidity Style Guide, Non-external/public variable and function names should begin with an [underscore](https://docs.soliditylang.org/en/latest/style-guide.html#underscore-prefix-for-non-external-functions-and-variables)


### Number Of Instances Found
67

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

126:    uint40 private constant DISABLE_RAGEQUIT_PERMANENTLY = 0xab2cb21860; // uint40(uint256(keccak256("DISABLE_RAGEQUIT_PERMANENTLY")))	// @audit-issue
```
*GitHub*: [126](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L126-L126)
```solidity
Path: ./contracts/crowdfund/AtomicManualParty.sol

33:    IPartyFactory private immutable PARTY_FACTORY;	// @audit-issue
```
*GitHub*: [33](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L33-L33)
```solidity
Path: ./contracts/globals/LibGlobals.sol

14:    uint256 internal constant GLOBAL_PARTY_IMPL = 1;	// @audit-issue

15:    uint256 internal constant GLOBAL_PROPOSAL_ENGINE_IMPL = 2;	// @audit-issue

16:    uint256 internal constant GLOBAL_PARTY_FACTORY = 3;	// @audit-issue

17:    uint256 internal constant GLOBAL_GOVERNANCE_NFT_RENDER_IMPL = 4;	// @audit-issue

18:    uint256 internal constant GLOBAL_CF_NFT_RENDER_IMPL = 5;	// @audit-issue

19:    uint256 internal constant GLOBAL_OS_ZORA_AUCTION_TIMEOUT = 6;	// @audit-issue

20:    uint256 internal constant GLOBAL_OS_ZORA_AUCTION_DURATION = 7;	// @audit-issue

24:    uint256 internal constant GLOBAL_DAO_WALLET = 11;	// @audit-issue

25:    uint256 internal constant GLOBAL_TOKEN_DISTRIBUTOR = 12;	// @audit-issue

26:    uint256 internal constant GLOBAL_OPENSEA_CONDUIT_KEY = 13;	// @audit-issue

27:    uint256 internal constant GLOBAL_OPENSEA_ZONE = 14;	// @audit-issue

28:    uint256 internal constant GLOBAL_PROPOSAL_MAX_CANCEL_DURATION = 15;	// @audit-issue

29:    uint256 internal constant GLOBAL_ZORA_MIN_AUCTION_DURATION = 16;	// @audit-issue

30:    uint256 internal constant GLOBAL_ZORA_MAX_AUCTION_DURATION = 17;	// @audit-issue

31:    uint256 internal constant GLOBAL_ZORA_MAX_AUCTION_TIMEOUT = 18;	// @audit-issue

32:    uint256 internal constant GLOBAL_OS_MIN_ORDER_DURATION = 19;	// @audit-issue

33:    uint256 internal constant GLOBAL_OS_MAX_ORDER_DURATION = 20;	// @audit-issue

34:    uint256 internal constant GLOBAL_DISABLE_PARTY_ACTIONS = 21;	// @audit-issue

35:    uint256 internal constant GLOBAL_RENDERER_STORAGE = 22;	// @audit-issue

36:    uint256 internal constant GLOBAL_PROPOSAL_MIN_CANCEL_DURATION = 23;	// @audit-issue

39:    uint256 internal constant GLOBAL_METADATA_REGISTRY = 26;	// @audit-issue

43:    uint256 internal constant GLOBAL_SEAPORT = 30;	// @audit-issue

44:    uint256 internal constant GLOBAL_CONDUIT_CONTROLLER = 31;	// @audit-issue

45:    uint256 internal constant GLOBAL_OFF_CHAIN_SIGNATURE_VALIDATOR = 32;	// @audit-issue
```
*GitHub*: [14](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L14-L14), [15](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L15-L15), [16](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L16-L16), [17](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L17-L17), [18](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L18-L18), [19](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L19-L19), [20](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L20-L20), [24](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L24-L24), [25](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L25-L25), [26](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L26-L26), [27](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L27-L27), [28](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L28-L28), [29](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L29-L29), [30](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L30-L30), [31](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L31-L31), [32](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L32-L32), [33](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L33-L33), [34](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L34-L34), [35](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L35-L35), [36](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L36-L36), [39](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L39-L39), [43](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L43-L43), [44](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L44-L44), [45](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/LibGlobals.sol#L45-L45)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

34:    uint40 private constant ENABLE_RAGEQUIT_PERMANENTLY = 0x6b5b567bfe; // uint40(uint256(keccak256("ENABLE_RAGEQUIT_PERMANENTLY")))	// @audit-issue

35:    uint40 private constant DISABLE_RAGEQUIT_PERMANENTLY = 0xab2cb21860; // uint40(uint256(keccak256("DISABLE_RAGEQUIT_PERMANENTLY")))	// @audit-issue

38:    address private constant ETH_ADDRESS = 0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE;	// @audit-issue
```
*GitHub*: [34](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L34-L34), [35](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L35-L35), [38](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L38-L38)
```solidity
Path: ./contracts/party/PartyGovernance.sol

186:    uint256 private constant UINT40_HIGH_BIT = 1 << 39;	// @audit-issue

187:    uint96 private constant VETO_VALUE = type(uint96).max;	// @audit-issue
```
*GitHub*: [186](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L186-L186), [187](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L187-L187)
```solidity
Path: ./contracts/utils/LibRawResult.sol

6:    function rawRevert(bytes memory b) internal pure {

13:    function rawReturn(bytes memory b) internal pure {
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRawResult.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRawResult.sol#L-L)
```solidity
Path: ./contracts/utils/LibSafeERC721.sol

16:    function safeOwnerOf(IERC721 token, uint256 tokenId) internal view returns (address owner) {
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeERC721.sol#L-L)
```solidity
Path: ./contracts/utils/LibERC20Compat.sol

13:    function compatTransfer(IERC20 token, address to, uint256 amount) internal {

36:    function compatTransferFrom(IERC20 token, address from, address to, uint256 amount) internal {

58:    function compatApprove(IERC20 token, address spender, uint256 amount) internal {
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibERC20Compat.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibERC20Compat.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibERC20Compat.sol#L-L)
```solidity
Path: ./contracts/utils/LibAddress.sol

8:    function transferEth(address payable receiver, uint256 amount) internal {
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibAddress.sol#L-L)
```solidity
Path: ./contracts/utils/LibSafeCast.sol

12:    function safeCastUint256ToUint96(uint256 v) internal pure returns (uint96) {

19:    function safeCastUint256ToUint128(uint256 v) internal pure returns (uint128) {

26:    function safeCastUint256ToInt192(uint256 v) internal pure returns (int192) {

33:    function safeCastUint96ToInt192(uint96 v) internal pure returns (int192) {

37:    function safeCastInt192ToUint96(int192 i192) internal pure returns (uint96) {

44:    function safeCastUint256ToInt128(uint256 x) internal pure returns (int128) {

51:    function safeCastUint256ToUint40(uint256 x) internal pure returns (uint40) {
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeCast.sol#L-L)
```solidity
Path: ./contracts/utils/PartyHelpers.sol

32:    address private constant ETH_ADDRESS = 0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE;	// @audit-issue
```
*GitHub*: [32](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/PartyHelpers.sol#L32-L32)
```solidity
Path: ./contracts/utils/vendor/Base64.sol

9:    bytes internal constant TABLE =	// @audit-issue

13:    function encode(bytes memory data) internal pure returns (string memory) {
```
*GitHub*: [9](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Base64.sol#L9-L9), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Base64.sol#L-L)
```solidity
Path: ./contracts/utils/vendor/Strings.sol

12:    function toString(uint256 value) internal pure returns (string memory) {

37:    function toHexString(uint256 value) internal pure returns (string memory) {

53:    function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {

68:    function toHexString(address addr) internal pure returns (string memory) {

72:    function substring(
73:        string memory str,
74:        uint startIndex,
75:        uint endIndex
76:    ) internal pure returns (string memory) {
```
*GitHub*: [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L-L)
```solidity
Path: ./contracts/proposals/LibProposal.sol

7:    uint256 internal constant PROPOSAL_FLAG_UNANIMOUS = 0x1;	// @audit-issue

8:    uint256 internal constant PROPOSAL_FLAG_HOSTS_ACCEPT = 0x2;	// @audit-issue

10:    function isTokenPrecious(
11:        IERC721 token,
12:        IERC721[] memory preciousTokens
13:    ) internal pure returns (bool) {

22:    function isTokenIdPrecious(
23:        IERC721 token,
24:        uint256 tokenId,
25:        IERC721[] memory preciousTokens,
26:        uint256[] memory preciousTokenIds
27:    ) internal pure returns (bool) {
```
*GitHub*: [7](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/LibProposal.sol#L7-L7), [8](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/LibProposal.sol#L8-L8), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/LibProposal.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/LibProposal.sol#L-L)
```solidity
Path: ./contracts/operators/ERC20SwapOperator.sol

61:    IERC20 private constant ETH_TOKEN_ADDRESS = IERC20(0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE);	// @audit-issue
```
*GitHub*: [61](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L61-L61)
```solidity
Path: ./contracts/vendor/solmate/ERC20.sol

39:    uint256 internal immutable INITIAL_CHAIN_ID;	// @audit-issue

41:    bytes32 internal immutable INITIAL_DOMAIN_SEPARATOR;	// @audit-issue

157:    function computeDomainSeparator() internal view virtual returns (bytes32) {
```
*GitHub*: [39](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC20.sol#L39-L39), [41](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC20.sol#L41-L41), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC20.sol#L-L)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

60:    address private constant NATIVE_TOKEN_ADDRESS = 0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE;	// @audit-issue
```
*GitHub*: [60](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L60-L60)
```solidity
Path: ./deploy/LibDeployConstants.sol

5:    uint256 internal constant PARTY_DAO_DISTRIBUTION_SPLIT_BPS = 250;	// @audit-issue

34:    function goerli(address multisig) internal pure returns (DeployConstants memory) {

68:    function baseGoerli(address multisig) internal pure returns (DeployConstants memory) {

102:    function mainnet() internal pure returns (DeployConstants memory) {

136:    function base() internal pure returns (DeployConstants memory) {
```
*GitHub*: [5](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L5-L5), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L-L), [](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L-L)
</details>



### [N-03] Large numeric literals should use underscores for readability
### Severity
* Impact: Low
* Likelihood: Low
### Description


### Number Of Instances Found
36

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

98:            interfaceId == 0x80ac58cd ||	// @audit-issue

100:            interfaceId == 0x49064906;	// @audit-issue
```
*GitHub*: [98](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L98-L98), [100](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L100-L100)
```solidity
Path: ./contracts/party/PartyGovernance.sol

338:            interfaceId == 0x49064906;	// @audit-issue
```
*GitHub*: [338](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L338-L338)
```solidity
Path: ./contracts/proposals/SetGovernanceParameterProposal.sol

54:            if (proposalData.passThresholdBps > 10000) {	// @audit-issue
```
*GitHub*: [54](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/SetGovernanceParameterProposal.sol#L54-L54)
```solidity
Path: ./contracts/vendor/solmate/ERC1155.sol

138:            interfaceId == 0x01ffc9a7 || // ERC165 Interface ID for ERC165	// @audit-issue

139:            interfaceId == 0xd9b67a26 || // ERC165 Interface ID for ERC1155	// @audit-issue

140:            interfaceId == 0x0e89341c; // ERC165 Interface ID for ERC1155MetadataURI	// @audit-issue
```
*GitHub*: [138](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L138-L138), [139](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L139-L139), [140](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L140-L140)
```solidity
Path: ./contracts/vendor/solmate/ERC721.sol

141:            interfaceId == 0x80ac58cd || // ERC165 Interface ID for ERC721	// @audit-issue

142:            interfaceId == 0x5b5e139f; // ERC165 Interface ID for ERC721Metadata	// @audit-issue
```
*GitHub*: [141](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L141-L141), [142](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L142-L142)
```solidity
Path: ./contracts/signature-validators/OffChainSignatureValidator.sol

60:            10000;	// @audit-issue
```
*GitHub*: [60](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L60-L60)
```solidity
Path: ./deploy/LibDeployConstants.sol

36:        allowedERC20SwapOperatorTargets[0] = 0xF91bB752490473B8342a3E964E855b9f9a2A668e; // 0x Swap Aggregator	// @audit-issue

39:            seaportExchangeAddress: 0x00000000000000ADc04C56Bf30aC9d3c0aAF14dC,	// @audit-issue

53:            osConduitKey: 0xf984c55ca75735630c1c27d3d06969c1aa6af1df86d22ddc0e3a978ad6138e9f,	// @audit-issue

54:            osConduitController: 0x00000000F9490004C11Cef243f5400493c00Ad63,	// @audit-issue

55:            fractionalVaultFactory: 0x014850E83d9D0D1BB0c8624035F09626b967B81c,	// @audit-issue

56:            nounsAuctionHouse: 0x7295e70f2B26986Ba108bD1Bf9E349a181F4a6Ea,	// @audit-issue

57:            zoraReserveAuctionCoreEth: 0x2506D9F5A2b0E1A2619bCCe01CD3e7C289A13163,	// @audit-issue

70:        allowedERC20SwapOperatorTargets[0] = 0xF91bB752490473B8342a3E964E855b9f9a2A668e; // 0x Swap Aggregator	// @audit-issue

73:            seaportExchangeAddress: 0x00000000000000ADc04C56Bf30aC9d3c0aAF14dC,	// @audit-issue

87:            osConduitKey: 0xf984c55ca75735630c1c27d3d06969c1aa6af1df86d22ddc0e3a978ad6138e9f,	// @audit-issue

88:            osConduitController: 0x00000000F9490004C11Cef243f5400493c00Ad63,	// @audit-issue

104:        allowedERC20SwapOperatorTargets[0] = 0xDef1C0ded9bec7F1a1670819833240f027b25EfF; // 0x Swap Aggregator	// @audit-issue

107:            seaportExchangeAddress: 0x00000000000000ADc04C56Bf30aC9d3c0aAF14dC,	// @audit-issue

118:            partyDaoMultisig: 0xF7f52Dd34bc21eDA08c0b804C7c1dbc48375820f,	// @audit-issue

121:            osConduitKey: 0xf984c55ca75735630c1c27d3d06969c1aa6af1df86d22ddc0e3a978ad6138e9f,	// @audit-issue

122:            osConduitController: 0x00000000F9490004C11Cef243f5400493c00Ad63,	// @audit-issue

123:            fractionalVaultFactory: 0x85Aa7f78BdB2DE8F3e0c0010d99AD5853fFcfC63,	// @audit-issue

124:            nounsAuctionHouse: 0x830BD73E4184ceF73443C15111a1DF14e495C706,	// @audit-issue

125:            zoraReserveAuctionCoreEth: 0x5f7072E1fA7c01dfAc7Cf54289621AFAaD2184d0,	// @audit-issue

127:            deployedNounsMarketWrapper: 0x9319DAd8736D752C5c72DB229f8e1b280DC80ab1,	// @audit-issue

129:            oldTokenDistributor: 0x1CA2007a81F8A7491BB6E11D8e357FD810896454,	// @audit-issue

138:        allowedERC20SwapOperatorTargets[0] = 0xDef1C0ded9bec7F1a1670819833240f027b25EfF; // 0x Swap Aggregator	// @audit-issue

141:            seaportExchangeAddress: 0x00000000000000ADc04C56Bf30aC9d3c0aAF14dC,	// @audit-issue

152:            partyDaoMultisig: 0xF498fd75Ee8D35294952343f1A77CAE5EA5aF6AA,	// @audit-issue

155:            osConduitKey: 0xf984c55ca75735630c1c27d3d06969c1aa6af1df86d22ddc0e3a978ad6138e9f,	// @audit-issue

156:            osConduitController: 0x00000000F9490004C11Cef243f5400493c00Ad63,	// @audit-issue
```
*GitHub*: [36](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L36-L36), [39](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L39-L39), [53](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L53-L53), [54](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L54-L54), [55](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L55-L55), [56](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L56-L56), [57](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L57-L57), [70](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L70-L70), [73](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L73-L73), [87](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L87-L87), [88](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L88-L88), [104](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L104-L104), [107](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L107-L107), [118](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L118-L118), [121](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L121-L121), [122](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L122-L122), [123](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L123-L123), [124](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L124-L124), [125](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L125-L125), [127](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L127-L127), [129](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L129-L129), [138](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L138-L138), [141](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L141-L141), [152](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L152-L152), [155](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L155-L155), [156](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./deploy/LibDeployConstants.sol#L156-L156)
</details>



### [N-04] `else`-block not required
### Severity
* Impact: Low
* Likelihood: Low
### Description

One level of nesting can be removed by not having an `else` block when the `if`-block returns, and `if (foo) { return 1; } else { return 2; }` becomes `if (foo) { return 1; } return 2;`


### Number Of Instances Found
30

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/InitialETHCrowdfund.sol

384:        if (address(customMetadataProvider) == address(0)) {
385:            return
386:                opts.governanceOpts.partyFactory.createParty(
387:                    opts.governanceOpts.partyImpl,
388:                    authorities,
389:                    Party.PartyOptions({
390:                        name: opts.name,
391:                        symbol: opts.symbol,
392:                        customizationPresetId: opts.customizationPresetId,
393:                        governance: PartyGovernance.GovernanceOpts({
394:                            hosts: opts.governanceOpts.hosts,
395:                            voteDuration: opts.governanceOpts.voteDuration,
396:                            executionDelay: opts.governanceOpts.executionDelay,
397:                            passThresholdBps: opts.governanceOpts.passThresholdBps,
398:                            totalVotingPower: 0,
399:                            feeBps: opts.governanceOpts.feeBps,
400:                            feeRecipient: opts.governanceOpts.feeRecipient
401:                        }),
402:                        proposalEngine: opts.proposalEngineOpts
403:                    }),
404:                    opts.preciousTokens,
405:                    opts.preciousTokenIds,
406:                    opts.rageQuitTimestamp
407:                );
408:        } else {	// @audit-issue
409:            return
410:                opts.governanceOpts.partyFactory.createPartyWithMetadata(
411:                    opts.governanceOpts.partyImpl,
412:                    authorities,
413:                    Party.PartyOptions({
414:                        name: opts.name,
415:                        symbol: opts.symbol,
416:                        customizationPresetId: opts.customizationPresetId,
417:                        governance: PartyGovernance.GovernanceOpts({
418:                            hosts: opts.governanceOpts.hosts,
419:                            voteDuration: opts.governanceOpts.voteDuration,
420:                            executionDelay: opts.governanceOpts.executionDelay,
421:                            passThresholdBps: opts.governanceOpts.passThresholdBps,
422:                            totalVotingPower: 0,
423:                            feeBps: opts.governanceOpts.feeBps,
424:                            feeRecipient: opts.governanceOpts.feeRecipient
425:                        }),
426:                        proposalEngine: opts.proposalEngineOpts
427:                    }),
428:                    opts.preciousTokens,
429:                    opts.preciousTokenIds,
430:                    opts.rageQuitTimestamp,
431:                    customMetadataProvider,
432:                    customMetadata
433:                );
434:        }
```
*GitHub*: [408](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L408-L408)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

186:            if (totalContributions >= minTotalContributions) {
187:                return CrowdfundLifecycle.Won;
188:            } else {	// @audit-issue
189:                return CrowdfundLifecycle.Lost;
190:            }
```
*GitHub*: [188](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L188-L188)
```solidity
Path: ./contracts/utils/LibRenderer.sol

49:        if (n < 10 ** (decimals - 2)) {
50:            return "&lt;0.01";
51:        } else if (n < oneUnit) {	// @audit-issue
52:            // Preserve leading zeros for decimals.
53:            // (e.g. if 0.01, `n` will "1" so we need to prepend a "0").
54:            return
55:                string.concat("0.", prependNumWithZeros(str, decimals).substring(0, maxChars - 1));
56:        } else if (n >= 1000 * oneUnit) {
57:            return str.substring(0, maxChars);
58:        } else {
59:            uint256 i = bytes((n / oneUnit).toString()).length;
60:            return string.concat(str.substring(0, i), ".", str.substring(i, maxChars));
61:        }

51:        } else if (n < oneUnit) {
52:            // Preserve leading zeros for decimals.
53:            // (e.g. if 0.01, `n` will "1" so we need to prepend a "0").
54:            return
55:                string.concat("0.", prependNumWithZeros(str, decimals).substring(0, maxChars - 1));
56:        } else if (n >= 1000 * oneUnit) {	// @audit-issue
57:            return str.substring(0, maxChars);
58:        } else {
59:            uint256 i = bytes((n / oneUnit).toString()).length;
60:            return string.concat(str.substring(0, i), ".", str.substring(i, maxChars));
61:        }

56:        } else if (n >= 1000 * oneUnit) {
57:            return str.substring(0, maxChars);
58:        } else {	// @audit-issue
59:            uint256 i = bytes((n / oneUnit).toString()).length;
60:            return string.concat(str.substring(0, i), ".", str.substring(i, maxChars));
61:        }

83:            if (colorType == ColorType.PRIMARY) {
84:                return "#A7B8CF";
85:            } else if (colorType == ColorType.SECONDARY) {	// @audit-issue
86:                return "#DCE5F0";
87:            } else if (colorType == ColorType.LIGHT) {
88:                return "#91A6C3";
89:            } else if (colorType == ColorType.DARK) {
90:                return "#50586D";
91:            }

85:            } else if (colorType == ColorType.SECONDARY) {
86:                return "#DCE5F0";
87:            } else if (colorType == ColorType.LIGHT) {	// @audit-issue
88:                return "#91A6C3";
89:            } else if (colorType == ColorType.DARK) {
90:                return "#50586D";
91:            }

87:            } else if (colorType == ColorType.LIGHT) {
88:                return "#91A6C3";
89:            } else if (colorType == ColorType.DARK) {	// @audit-issue
90:                return "#50586D";
91:            }

93:            if (colorType == ColorType.PRIMARY) {
94:                return "#10B173";
95:            } else if (colorType == ColorType.SECONDARY) {	// @audit-issue
96:                return "#93DCB7";
97:            } else if (colorType == ColorType.LIGHT) {
98:                return "#00A25A";
99:            } else if (colorType == ColorType.DARK) {
100:                return "#005E3B";
101:            }

95:            } else if (colorType == ColorType.SECONDARY) {
96:                return "#93DCB7";
97:            } else if (colorType == ColorType.LIGHT) {	// @audit-issue
98:                return "#00A25A";
99:            } else if (colorType == ColorType.DARK) {
100:                return "#005E3B";
101:            }

97:            } else if (colorType == ColorType.LIGHT) {
98:                return "#00A25A";
99:            } else if (colorType == ColorType.DARK) {	// @audit-issue
100:                return "#005E3B";
101:            }

103:            if (colorType == ColorType.PRIMARY) {
104:                return "#00C1FA";
105:            } else if (colorType == ColorType.SECONDARY) {	// @audit-issue
106:                return "#B1EFFD";
107:            } else if (colorType == ColorType.LIGHT) {
108:                return "#00B4EA";
109:            } else if (colorType == ColorType.DARK) {
110:                return "#005669";
111:            }

105:            } else if (colorType == ColorType.SECONDARY) {
106:                return "#B1EFFD";
107:            } else if (colorType == ColorType.LIGHT) {	// @audit-issue
108:                return "#00B4EA";
109:            } else if (colorType == ColorType.DARK) {
110:                return "#005669";
111:            }

107:            } else if (colorType == ColorType.LIGHT) {
108:                return "#00B4EA";
109:            } else if (colorType == ColorType.DARK) {	// @audit-issue
110:                return "#005669";
111:            }

113:            if (colorType == ColorType.PRIMARY) {
114:                return "#2C78F3";
115:            } else if (colorType == ColorType.SECONDARY) {	// @audit-issue
116:                return "#B3D4FF";
117:            } else if (colorType == ColorType.LIGHT) {
118:                return "#0E70E0";
119:            } else if (colorType == ColorType.DARK) {
120:                return "#00286A";
121:            }

115:            } else if (colorType == ColorType.SECONDARY) {
116:                return "#B3D4FF";
117:            } else if (colorType == ColorType.LIGHT) {	// @audit-issue
118:                return "#0E70E0";
119:            } else if (colorType == ColorType.DARK) {
120:                return "#00286A";
121:            }

117:            } else if (colorType == ColorType.LIGHT) {
118:                return "#0E70E0";
119:            } else if (colorType == ColorType.DARK) {	// @audit-issue
120:                return "#00286A";
121:            }

123:            if (colorType == ColorType.PRIMARY) {
124:                return "#9B45DF";
125:            } else if (colorType == ColorType.SECONDARY) {	// @audit-issue
126:                return "#D2ACF2";
127:            } else if (colorType == ColorType.LIGHT) {
128:                return "#832EC9";
129:            } else if (colorType == ColorType.DARK) {
130:                return "#47196B";
131:            }

125:            } else if (colorType == ColorType.SECONDARY) {
126:                return "#D2ACF2";
127:            } else if (colorType == ColorType.LIGHT) {	// @audit-issue
128:                return "#832EC9";
129:            } else if (colorType == ColorType.DARK) {
130:                return "#47196B";
131:            }

127:            } else if (colorType == ColorType.LIGHT) {
128:                return "#832EC9";
129:            } else if (colorType == ColorType.DARK) {	// @audit-issue
130:                return "#47196B";
131:            }

133:            if (colorType == ColorType.PRIMARY) {
134:                return "#FF6BF3";
135:            } else if (colorType == ColorType.SECONDARY) {	// @audit-issue
136:                return "#FFC8FB";
137:            } else if (colorType == ColorType.LIGHT) {
138:                return "#E652E2";
139:            } else if (colorType == ColorType.DARK) {
140:                return "#911A96";
141:            }

135:            } else if (colorType == ColorType.SECONDARY) {
136:                return "#FFC8FB";
137:            } else if (colorType == ColorType.LIGHT) {	// @audit-issue
138:                return "#E652E2";
139:            } else if (colorType == ColorType.DARK) {
140:                return "#911A96";
141:            }

137:            } else if (colorType == ColorType.LIGHT) {
138:                return "#E652E2";
139:            } else if (colorType == ColorType.DARK) {	// @audit-issue
140:                return "#911A96";
141:            }

143:            if (colorType == ColorType.PRIMARY) {
144:                return "#FF8946";
145:            } else if (colorType == ColorType.SECONDARY) {	// @audit-issue
146:                return "#FFE38B";
147:            } else if (colorType == ColorType.LIGHT) {
148:                return "#E47B2F";
149:            } else if (colorType == ColorType.DARK) {
150:                return "#732700";
151:            }

145:            } else if (colorType == ColorType.SECONDARY) {
146:                return "#FFE38B";
147:            } else if (colorType == ColorType.LIGHT) {	// @audit-issue
148:                return "#E47B2F";
149:            } else if (colorType == ColorType.DARK) {
150:                return "#732700";
151:            }

147:            } else if (colorType == ColorType.LIGHT) {
148:                return "#E47B2F";
149:            } else if (colorType == ColorType.DARK) {	// @audit-issue
150:                return "#732700";
151:            }

153:            if (colorType == ColorType.PRIMARY) {
154:                return "#EC0000";
155:            } else if (colorType == ColorType.SECONDARY) {	// @audit-issue
156:                return "#FFA6A6";
157:            } else if (colorType == ColorType.LIGHT) {
158:                return "#D70000";
159:            } else if (colorType == ColorType.DARK) {
160:                return "#6F0000";
161:            }

155:            } else if (colorType == ColorType.SECONDARY) {
156:                return "#FFA6A6";
157:            } else if (colorType == ColorType.LIGHT) {	// @audit-issue
158:                return "#D70000";
159:            } else if (colorType == ColorType.DARK) {
160:                return "#6F0000";
161:            }

157:            } else if (colorType == ColorType.LIGHT) {
158:                return "#D70000";
159:            } else if (colorType == ColorType.DARK) {	// @audit-issue
160:                return "#6F0000";
161:            }
```
*GitHub*: [51](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L51-L51), [56](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L56-L56), [58](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L58-L58), [85](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L85-L85), [87](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L87-L87), [89](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L89-L89), [95](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L95-L95), [97](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L97-L97), [99](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L99-L99), [105](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L105-L105), [107](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L107-L107), [109](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L109-L109), [115](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L115-L115), [117](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L117-L117), [119](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L119-L119), [125](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L125-L125), [127](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L127-L127), [129](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L129-L129), [135](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L135-L135), [137](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L137-L137), [139](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L139-L139), [145](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L145-L145), [147](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L147-L147), [149](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L149-L149), [155](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L155-L155), [157](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L157-L157), [159](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L159-L159)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

259:        if (success && abi.decode(response, (uint16)) >= 1) {
260:            uint256 shareOfSupply = ((info.party.getDistributionShareOf(partyTokenId)) * 1e18) /
261:                info.totalShares;
262:
263:            return
264:                // We round up here to prevent dust amounts getting trapped in this contract.
265:                ((shareOfSupply * info.memberSupply + (1e18 - 1)) / 1e18)
266:                    .safeCastUint256ToUint128();
267:        } else {	// @audit-issue
268:            // Use method of calculating claim amount for backwards
269:            // compatibility with older parties where getDistributionShareOf()
270:            // returned the fraction of the memberSupply partyTokenId is
271:            // entitled to, scaled by 1e18.
272:            uint256 shareOfSupply = party.getDistributionShareOf(partyTokenId);
273:
274:            return
275:                // We round up here to prevent dust amounts getting trapped in this contract.
276:                ((shareOfSupply * info.memberSupply + (1e18 - 1)) / 1e18)
277:                    .safeCastUint256ToUint128();
278:        }
```
*GitHub*: [267](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L267-L267)
</details>



### [N-05] Adding a return statement when the function defines a named return variable, is redundant
### Severity
* Impact: Low
* Likelihood: Low
### Description

Once the return variable has been assigned (or has its default value), there is no need to explicitly return it at the end of the function, since it's returned automatically.

### Number Of Instances Found
64

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/InitialETHCrowdfund.sol

164:    function contribute(
165:        address delegate,
166:        bytes memory gateData
167:    ) public payable onlyDelegateCall returns (uint96 votingPower) {
168:        return	// @audit-issue
169:            _contribute(
170:                payable(msg.sender),
171:                delegate,
172:                msg.value.safeCastUint256ToUint96(),
173:                0, // Mint a new party card for the contributor.
174:                gateData
175:            );
176:    }

185:    function contribute(
186:        uint256 tokenId,
187:        address delegate,
188:        bytes memory gateData
189:    ) public payable onlyDelegateCall returns (uint96 votingPower) {
190:        return	// @audit-issue
191:            _contribute(
192:                payable(msg.sender),
193:                delegate,
194:                msg.value.safeCastUint256ToUint96(),
195:                tokenId,
196:                gateData
197:            );
198:    }

235:    function contributeFor(
236:        uint256 tokenId,
237:        address payable recipient,
238:        address initialDelegate,
239:        bytes memory gateData
240:    ) external payable onlyDelegateCall returns (uint96 votingPower) {
241:        return	// @audit-issue
242:            _contribute(
243:                recipient,
244:                initialDelegate,
245:                msg.value.safeCastUint256ToUint96(),
246:                tokenId,
247:                gateData
248:            );
249:    }

275:    function _contribute(
276:        address payable contributor,
277:        address delegate,
278:        uint96 amount,
279:        uint256 tokenId,
280:        bytes memory gateData
281:    ) private returns (uint96 votingPower) {
282:        // Require a non-null delegate.
283:        if (delegate == address(0)) {
284:            revert InvalidDelegateError();
285:        }
286:
287:        // Must not be blocked by gatekeeper.
288:        IGateKeeper _gateKeeper = gateKeeper;
289:        if (_gateKeeper != IGateKeeper(address(0))) {
290:            if (!_gateKeeper.isAllowed(msg.sender, gateKeeperId, gateData)) {
291:                revert NotAllowedByGateKeeperError(msg.sender, _gateKeeper, gateKeeperId, gateData);
292:            }
293:        }
294:
295:        votingPower = _processContribution(contributor, delegate, amount);
296:
297:        // OK to contribute with zero just to update delegate.
298:        if (amount == 0) return 0;	// @audit-issue
299:
300:        if (tokenId == 0) {
301:            // Mint contributor a new party card.
302:            party.mint(contributor, votingPower, delegate);
303:        } else if (disableContributingForExistingCard) {
304:            revert ContributingForExistingCardDisabledError();
305:        } else if (party.ownerOf(tokenId) == contributor) {
306:            // Increase voting power of contributor's existing party card.
307:            party.increaseVotingPower(tokenId, votingPower);
308:        } else {
309:            revert NotOwnerError(tokenId);
310:        }
311:    }
```
*GitHub*: [168](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L168-L168), [190](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L190-L190), [241](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L241-L241), [298](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L298-L298)
```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

122:    function balanceOf(address owner) external view returns (uint256 numTokens) {
123:        return _doesTokenExistFor(owner) ? 1 : 0;	// @audit-issue
124:    }
```
*GitHub*: [123](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L123-L123)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

453:    function _isHost(
454:        address who,
455:        FixedGovernanceOpts memory governanceOpts,
456:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
457:        uint256 hostIndex
458:    ) private view returns (bool isHost) {
459:        if (hostIndex < governanceOpts.hosts.length && who == governanceOpts.hosts[hostIndex]) {
460:            // Validate governance opts if the host was found.
461:            _assertValidOpts(governanceOpts, proposalEngineOpts);
462:            return true;	// @audit-issue
463:        }
464:        return false;
465:    }

453:    function _isHost(
454:        address who,
455:        FixedGovernanceOpts memory governanceOpts,
456:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
457:        uint256 hostIndex
458:    ) private view returns (bool isHost) {
459:        if (hostIndex < governanceOpts.hosts.length && who == governanceOpts.hosts[hostIndex]) {
460:            // Validate governance opts if the host was found.
461:            _assertValidOpts(governanceOpts, proposalEngineOpts);
462:            return true;
463:        }
464:        return false;	// @audit-issue
465:    }

467:    function _isPartyDao(address who) private view returns (bool isPartyDao) {
468:        return who == _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);	// @audit-issue
469:    }

537:    function _createParty(
538:        FixedGovernanceOpts memory governanceOpts,
539:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
540:        bool governanceOptsAlreadyValidated,
541:        IERC721 preciousToken,
542:        uint256 preciousTokenId
543:    ) internal returns (Party party_) {
544:        IERC721[] memory tokens = new IERC721[](1);
545:        tokens[0] = preciousToken;
546:        uint256[] memory tokenIds = new uint256[](1);
547:        tokenIds[0] = preciousTokenId;
548:        return	// @audit-issue
549:            _createParty(
550:                governanceOpts,
551:                proposalEngineOpts,
552:                governanceOptsAlreadyValidated,
553:                tokens,
554:                tokenIds
555:            );
556:    }

569:    function _hashOpts(
570:        FixedGovernanceOpts memory govOpts,
571:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts
572:    ) internal pure returns (bytes32 h) {
573:        return keccak256(abi.encode(govOpts, proposalEngineOpts));	// @audit-issue
574:    }
```
*GitHub*: [462](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L462-L462), [464](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L464-L464), [468](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L468-L468), [548](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L548-L548), [573](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L573-L573)
```solidity
Path: ./contracts/crowdfund/CrowdfundFactory.sol

160:    function createInitialETHCrowdfund(
161:        InitialETHCrowdfund crowdfundImpl,
162:        InitialETHCrowdfund.InitialETHCrowdfundOptions memory crowdfundOpts,
163:        InitialETHCrowdfund.ETHPartyOptions memory partyOpts,
164:        bytes memory createGateCallData
165:    ) external payable returns (InitialETHCrowdfund inst) {
166:        return	// @audit-issue
167:            createInitialETHCrowdfundWithMetadata(
168:                crowdfundImpl,
169:                crowdfundOpts,
170:                partyOpts,
171:                MetadataProvider(address(0)),
172:                "",
173:                createGateCallData
174:            );
175:    }

225:    function _prepareGate(
226:        IGateKeeper gateKeeper,
227:        bytes12 gateKeeperId,
228:        bytes memory createGateCallData
229:    ) private returns (bytes12 newGateKeeperId) {
230:        if (address(gateKeeper) == address(0) || gateKeeperId != bytes12(0)) {
231:            // Using an existing gate on the gatekeeper
232:            // or not using a gate at all.
233:            return gateKeeperId;	// @audit-issue
234:        }
235:        // Call the gate creation function on the gatekeeper.
236:        (bool s, bytes memory r) = address(gateKeeper).call(createGateCallData);
237:        if (!s) {
238:            r.rawRevert();
239:        }
240:        // Result is always a bytes12.
241:        return abi.decode(r, (bytes12));
242:    }

225:    function _prepareGate(
226:        IGateKeeper gateKeeper,
227:        bytes12 gateKeeperId,
228:        bytes memory createGateCallData
229:    ) private returns (bytes12 newGateKeeperId) {
230:        if (address(gateKeeper) == address(0) || gateKeeperId != bytes12(0)) {
231:            // Using an existing gate on the gatekeeper
232:            // or not using a gate at all.
233:            return gateKeeperId;
234:        }
235:        // Call the gate creation function on the gatekeeper.
236:        (bool s, bytes memory r) = address(gateKeeper).call(createGateCallData);
237:        if (!s) {
238:            r.rawRevert();
239:        }
240:        // Result is always a bytes12.
241:        return abi.decode(r, (bytes12));	// @audit-issue
242:    }
```
*GitHub*: [166](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L166-L166), [233](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L233-L233), [241](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L241-L241)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

305:    function _getFinalPrice() internal view override returns (uint256 price) {
306:        return lastBid;	// @audit-issue
307:    }
```
*GitHub*: [306](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L306-L306)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfund.sol

122:    function buy(
123:        address payable callTarget,
124:        uint96 callValue,
125:        bytes memory callData,
126:        FixedGovernanceOpts memory governanceOpts,
127:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
128:        uint256 hostIndex
129:    ) external onlyDelegateCall returns (Party party_) {
130:        // This function can be optionally restricted in different ways.
131:        bool isValidatedGovernanceOpts;
132:        if (onlyHostCanBuy) {
133:            // Only a host can call this function.
134:            _assertIsHost(msg.sender, governanceOpts, proposalEngineOpts, hostIndex);
135:            // If _assertIsHost() succeeded, the governance opts were validated.
136:            isValidatedGovernanceOpts = true;
137:        } else if (address(gateKeeper) != address(0)) {
138:            // `onlyHostCanBuy` is false and we are using a gatekeeper.
139:            // Only a contributor can call this function.
140:            _assertIsContributor(msg.sender);
141:        }
142:        {
143:            // Ensure that the crowdfund is still active.
144:            CrowdfundLifecycle lc = getCrowdfundLifecycle();
145:            if (lc != CrowdfundLifecycle.Active) {
146:                revert WrongLifecycleError(lc);
147:            }
148:        }
149:
150:        // Temporarily set to non-zero as a reentrancy guard.
151:        settledPrice = type(uint96).max;
152:
153:        // Buy the NFT and check NFT is owned by the crowdfund.
154:        (bool success, bytes memory revertData) = _buy(
155:            nftContract,
156:            nftTokenId,
157:            callTarget,
158:            callValue,
159:            callData
160:        );
161:
162:        if (!success) {
163:            if (revertData.length > 0) {
164:                revertData.rawRevert();
165:            } else {
166:                revert FailedToBuyNFTError(nftContract, nftTokenId);
167:            }
168:        }
169:
170:        return	// @audit-issue
171:            _finalize(
172:                nftContract,
173:                nftTokenId,
174:                callValue,
175:                governanceOpts,
176:                proposalEngineOpts,
177:                isValidatedGovernanceOpts
178:            );
179:    }
```
*GitHub*: [170](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfund.sol#L170-L170)
```solidity
Path: ./contracts/crowdfund/CollectionBuyCrowdfund.sol

113:    function buy(
114:        uint256 tokenId,
115:        address payable callTarget,
116:        uint96 callValue,
117:        bytes memory callData,
118:        FixedGovernanceOpts memory governanceOpts,
119:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
120:        uint256 hostIndex
121:    ) external onlyDelegateCall returns (Party party_) {
122:        // This function is always restricted to hosts.
123:        _assertIsHost(msg.sender, governanceOpts, proposalEngineOpts, hostIndex);
124:
125:        {
126:            // Ensure that the crowdfund is still active.
127:            CrowdfundLifecycle lc = getCrowdfundLifecycle();
128:            if (lc != CrowdfundLifecycle.Active) {
129:                revert WrongLifecycleError(lc);
130:            }
131:        }
132:
133:        // Temporarily set to non-zero as a reentrancy guard.
134:        settledPrice = type(uint96).max;
135:
136:        // Buy the NFT and check NFT is owned by the crowdfund.
137:        (bool success, bytes memory revertData) = _buy(
138:            nftContract,
139:            tokenId,
140:            callTarget,
141:            callValue,
142:            callData
143:        );
144:
145:        if (!success) {
146:            if (revertData.length > 0) {
147:                revertData.rawRevert();
148:            } else {
149:                revert FailedToBuyNFTError(nftContract, tokenId);
150:            }
151:        }
152:
153:        return	// @audit-issue
154:            _finalize(
155:                nftContract,
156:                tokenId,
157:                callValue,
158:                governanceOpts,
159:                proposalEngineOpts,
160:                // If `_assertIsHost()` succeeded, the governance opts were validated.
161:                true
162:            );
163:    }
```
*GitHub*: [153](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBuyCrowdfund.sol#L153-L153)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

175:    function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {
176:        if (maxTotalContributions == 0) {
177:            return CrowdfundLifecycle.Invalid;	// @audit-issue
178:        }
179:
180:        uint256 expiry_ = expiry;
181:        if (expiry_ == 0) {
182:            return CrowdfundLifecycle.Finalized;
183:        }
184:
185:        if (block.timestamp >= expiry_) {
186:            if (totalContributions >= minTotalContributions) {
187:                return CrowdfundLifecycle.Won;
188:            } else {
189:                return CrowdfundLifecycle.Lost;
190:            }
191:        }
192:
193:        return CrowdfundLifecycle.Active;
194:    }

175:    function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {
176:        if (maxTotalContributions == 0) {
177:            return CrowdfundLifecycle.Invalid;
178:        }
179:
180:        uint256 expiry_ = expiry;
181:        if (expiry_ == 0) {
182:            return CrowdfundLifecycle.Finalized;	// @audit-issue
183:        }
184:
185:        if (block.timestamp >= expiry_) {
186:            if (totalContributions >= minTotalContributions) {
187:                return CrowdfundLifecycle.Won;
188:            } else {
189:                return CrowdfundLifecycle.Lost;
190:            }
191:        }
192:
193:        return CrowdfundLifecycle.Active;
194:    }

175:    function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {
176:        if (maxTotalContributions == 0) {
177:            return CrowdfundLifecycle.Invalid;
178:        }
179:
180:        uint256 expiry_ = expiry;
181:        if (expiry_ == 0) {
182:            return CrowdfundLifecycle.Finalized;
183:        }
184:
185:        if (block.timestamp >= expiry_) {
186:            if (totalContributions >= minTotalContributions) {
187:                return CrowdfundLifecycle.Won;	// @audit-issue
188:            } else {
189:                return CrowdfundLifecycle.Lost;
190:            }
191:        }
192:
193:        return CrowdfundLifecycle.Active;
194:    }

175:    function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {
176:        if (maxTotalContributions == 0) {
177:            return CrowdfundLifecycle.Invalid;
178:        }
179:
180:        uint256 expiry_ = expiry;
181:        if (expiry_ == 0) {
182:            return CrowdfundLifecycle.Finalized;
183:        }
184:
185:        if (block.timestamp >= expiry_) {
186:            if (totalContributions >= minTotalContributions) {
187:                return CrowdfundLifecycle.Won;
188:            } else {
189:                return CrowdfundLifecycle.Lost;
190:            }
191:        }
192:
193:        return CrowdfundLifecycle.Active;	// @audit-issue
194:    }

196:    function _processContribution(
197:        address payable contributor,
198:        address delegate,
199:        uint96 amount
200:    ) internal returns (uint96 votingPower) {
201:        address oldDelegate = delegationsByContributor[contributor];
202:        if (msg.sender == contributor || oldDelegate == address(0)) {
203:            // Update delegate.
204:            delegationsByContributor[contributor] = delegate;
205:        } else {
206:            // Prevent changing another's delegate if already delegated.
207:            delegate = oldDelegate;
208:        }
209:
210:        emit Contributed(msg.sender, contributor, amount, delegate);
211:
212:        // OK to contribute with zero just to update delegate.
213:        if (amount == 0) return 0;	// @audit-issue
214:
215:        // Only allow contributions while the crowdfund is active.
216:        CrowdfundLifecycle lc = getCrowdfundLifecycle();
217:        if (lc != CrowdfundLifecycle.Active) {
218:            revert WrongLifecycleError(lc);
219:        }
220:
221:        // Check that the contribution amount is at or below the maximum.
222:        uint96 maxContribution_ = maxContribution;
223:        if (amount > maxContribution_) {
224:            revert AboveMaximumContributionsError(amount, maxContribution_);
225:        }
226:
227:        uint96 newTotalContributions = totalContributions + amount;
228:        uint96 maxTotalContributions_ = maxTotalContributions;
229:        if (newTotalContributions >= maxTotalContributions_) {
230:            totalContributions = maxTotalContributions_;
231:
232:            // Finalize the crowdfund.
233:            // This occurs before refunding excess contribution to act as a
234:            // reentrancy guard.
235:            _finalize(maxTotalContributions_);
236:
237:            // Refund excess contribution.
238:            uint96 refundAmount = newTotalContributions - maxTotalContributions;
239:            if (refundAmount > 0) {
240:                amount -= refundAmount;
241:                payable(msg.sender).transferEth(refundAmount);
242:            }
243:        } else {
244:            totalContributions = newTotalContributions;
245:        }
246:
247:        // Check that the contribution amount is at or above the minimum. This
248:        // is done after `amount` is potentially reduced if refunding excess
249:        // contribution. There is a case where this prevents a crowdfunds from
250:        // reaching `maxTotalContributions` if the `minContribution` is greater
251:        // than the difference between `maxTotalContributions` and the current
252:        // `totalContributions`. In this scenario users will have to wait until
253:        // the crowdfund expires or a host finalizes after
254:        // `minTotalContribution` has been reached by calling `finalize()`.
255:        uint96 minContribution_ = minContribution;
256:        if (amount < minContribution_) {
257:            revert BelowMinimumContributionsError(amount, minContribution_);
258:        }
259:
260:        // Subtract fee from contribution amount if applicable.
261:        address payable fundingSplitRecipient_ = fundingSplitRecipient;
262:        uint16 fundingSplitBps_ = fundingSplitBps;
263:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
264:            // Removes funding split from contribution amount in a way that
265:            // avoids rounding errors for very small contributions <1e4 wei.
266:            amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
267:        }
268:
269:        // Calculate voting power.
270:        votingPower = (amount * exchangeRateBps) / 1e4;
271:
272:        if (votingPower == 0) revert ZeroVotingPowerError();
273:    }
```
*GitHub*: [177](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L177-L177), [182](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L182-L182), [187](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L187-L187), [193](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L193-L193), [213](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L213-L213)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfundBase.sol

101:    function _buy(
102:        IERC721 token,
103:        uint256 tokenId,
104:        address payable callTarget,
105:        uint96 callValue,
106:        bytes memory callData
107:    ) internal returns (bool success, bytes memory revertData) {
108:        // Check that the call is not prohibited.
109:        if (!_isCallAllowed(callTarget, callData, token)) {
110:            revert CallProhibitedError(callTarget, callData);
111:        }
112:        // Check that the call value is under the maximum price.
113:        {
114:            uint96 maximumPrice_ = maximumPrice;
115:            if (callValue > maximumPrice_) {
116:                revert MaximumPriceError(callValue, maximumPrice_);
117:            }
118:        }
119:        // Execute the call to buy the NFT.
120:        (bool s, bytes memory r) = callTarget.call{ value: callValue }(callData);
121:        if (!s) {
122:            return (false, r);	// @audit-issue
123:        }
124:        // Return whether the NFT was successfully bought.
125:        return (token.safeOwnerOf(tokenId) == address(this), "");
126:    }

101:    function _buy(
102:        IERC721 token,
103:        uint256 tokenId,
104:        address payable callTarget,
105:        uint96 callValue,
106:        bytes memory callData
107:    ) internal returns (bool success, bytes memory revertData) {
108:        // Check that the call is not prohibited.
109:        if (!_isCallAllowed(callTarget, callData, token)) {
110:            revert CallProhibitedError(callTarget, callData);
111:        }
112:        // Check that the call value is under the maximum price.
113:        {
114:            uint96 maximumPrice_ = maximumPrice;
115:            if (callValue > maximumPrice_) {
116:                revert MaximumPriceError(callValue, maximumPrice_);
117:            }
118:        }
119:        // Execute the call to buy the NFT.
120:        (bool s, bytes memory r) = callTarget.call{ value: callValue }(callData);
121:        if (!s) {
122:            return (false, r);
123:        }
124:        // Return whether the NFT was successfully bought.
125:        return (token.safeOwnerOf(tokenId) == address(this), "");	// @audit-issue
126:    }

168:    function _finalize(
169:        IERC721 token,
170:        uint256 tokenId,
171:        uint96 totalEthUsed,
172:        FixedGovernanceOpts memory governanceOpts,
173:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
174:        bool isValidatedGovernanceOpts
175:    ) internal returns (Party party_) {
176:        IERC721[] memory tokens = new IERC721[](1);
177:        tokens[0] = token;
178:        uint256[] memory tokenIds = new uint256[](1);
179:        tokenIds[0] = tokenId;
180:        return	// @audit-issue
181:            _finalize(
182:                tokens,
183:                tokenIds,
184:                totalEthUsed,
185:                governanceOpts,
186:                proposalEngineOpts,
187:                isValidatedGovernanceOpts
188:            );
189:    }

212:    function _isCallAllowed(
213:        address payable callTarget,
214:        bytes memory callData,
215:        IERC721 token
216:    ) private view returns (bool isAllowed) {
217:        // Ensure the call target isn't trying to reenter
218:        if (callTarget == address(this)) {
219:            return false;	// @audit-issue
220:        }
221:        if (callTarget == address(token) && callData.length >= 4) {
222:            // Get the function selector of the call (first 4 bytes of calldata).
223:            bytes4 selector;
224:            assembly {
225:                selector := and(
226:                    mload(add(callData, 32)),
227:                    0xffffffff00000000000000000000000000000000000000000000000000000000
228:                )
229:            }
230:            // Prevent approving the NFT to be transferred out from the crowdfund.
231:            if (
232:                selector == IERC721.approve.selector ||
233:                selector == IERC721.setApprovalForAll.selector
234:            ) {
235:                return false;
236:            }
237:        }
238:        // All other calls are allowed.
239:        return true;
240:    }

212:    function _isCallAllowed(
213:        address payable callTarget,
214:        bytes memory callData,
215:        IERC721 token
216:    ) private view returns (bool isAllowed) {
217:        // Ensure the call target isn't trying to reenter
218:        if (callTarget == address(this)) {
219:            return false;
220:        }
221:        if (callTarget == address(token) && callData.length >= 4) {
222:            // Get the function selector of the call (first 4 bytes of calldata).
223:            bytes4 selector;
224:            assembly {
225:                selector := and(
226:                    mload(add(callData, 32)),
227:                    0xffffffff00000000000000000000000000000000000000000000000000000000
228:                )
229:            }
230:            // Prevent approving the NFT to be transferred out from the crowdfund.
231:            if (
232:                selector == IERC721.approve.selector ||
233:                selector == IERC721.setApprovalForAll.selector
234:            ) {
235:                return false;	// @audit-issue
236:            }
237:        }
238:        // All other calls are allowed.
239:        return true;
240:    }

212:    function _isCallAllowed(
213:        address payable callTarget,
214:        bytes memory callData,
215:        IERC721 token
216:    ) private view returns (bool isAllowed) {
217:        // Ensure the call target isn't trying to reenter
218:        if (callTarget == address(this)) {
219:            return false;
220:        }
221:        if (callTarget == address(token) && callData.length >= 4) {
222:            // Get the function selector of the call (first 4 bytes of calldata).
223:            bytes4 selector;
224:            assembly {
225:                selector := and(
226:                    mload(add(callData, 32)),
227:                    0xffffffff00000000000000000000000000000000000000000000000000000000
228:                )
229:            }
230:            // Prevent approving the NFT to be transferred out from the crowdfund.
231:            if (
232:                selector == IERC721.approve.selector ||
233:                selector == IERC721.setApprovalForAll.selector
234:            ) {
235:                return false;
236:            }
237:        }
238:        // All other calls are allowed.
239:        return true;	// @audit-issue
240:    }
```
*GitHub*: [122](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L122-L122), [125](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L125-L125), [180](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L180-L180), [219](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L219-L219), [235](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L235-L235), [239](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L239-L239)
```solidity
Path: ./contracts/crowdfund/RollingAuctionCrowdfund.sol

81:    function finalize(
82:        FixedGovernanceOpts memory governanceOpts,
83:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts
84:    ) external onlyDelegateCall returns (Party party_) {
85:        // Empty args because we don't need to roll over to another auction.
86:        RollOverArgs memory args;
87:
88:        // If the crowdfund won, only `governanceOpts` is relevant. The rest are ignored.
89:        return finalizeOrRollOver(args, governanceOpts, proposalEngineOpts);	// @audit-issue
90:    }
```
*GitHub*: [89](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/RollingAuctionCrowdfund.sol#L89-L89)
```solidity
Path: ./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol

134:    function batchBuy(BatchBuyArgs memory args) external onlyDelegateCall returns (Party party_) {
135:        // This function is restricted to hosts.
136:        _assertIsHost(msg.sender, args.governanceOpts, args.proposalEngineOpts, args.hostIndex);
137:
138:        {
139:            // Ensure that the crowdfund is still active.
140:            CrowdfundLifecycle lc = getCrowdfundLifecycle();
141:            if (lc != CrowdfundLifecycle.Active) {
142:                revert WrongLifecycleError(lc);
143:            }
144:        }
145:
146:        if (args.minTokensBought == 0) {
147:            // Must buy at least one token.
148:            revert InvalidMinTokensBoughtError(0);
149:        }
150:
151:        // Check length of all arg arrays.
152:        if (
153:            args.tokenIds.length != args.callTargets.length ||
154:            args.tokenIds.length != args.callValues.length ||
155:            args.tokenIds.length != args.callDatas.length ||
156:            args.tokenIds.length != args.proofs.length
157:        ) {
158:            revert MismatchedCallArgLengthsError();
159:        }
160:
161:        // Temporarily set to non-zero as a reentrancy guard.
162:        settledPrice = type(uint96).max;
163:
164:        uint96 totalEthUsed;
165:        uint256 tokensBought;
166:        IERC721[] memory tokens = new IERC721[](args.tokenIds.length);
167:        IERC721 token = nftContract;
168:        bytes32 root = nftTokenIdsMerkleRoot;
169:        for (uint256 i; i < args.tokenIds.length; ++i) {
170:            if (root != bytes32(0)) {
171:                // Verify the token ID is in the merkle tree.
172:                _verifyTokenId(args.tokenIds[i], root, args.proofs[i]);
173:            }
174:
175:            // Used to ensure no ETH is spent if the call fails.
176:            uint256 balanceBefore = address(this).balance;
177:
178:            // Execute the call to buy the NFT.
179:            (bool success, bytes memory revertData) = _buy(
180:                token,
181:                args.tokenIds[i],
182:                args.callTargets[i],
183:                args.callValues[i],
184:                args.callDatas[i]
185:            );
186:
187:            if (!success) {
188:                if (args.minTokensBought >= args.tokenIds.length) {
189:                    // If the call failed with revert data, revert with that data.
190:                    if (revertData.length > 0) {
191:                        revertData.rawRevert();
192:                    } else {
193:                        revert FailedToBuyNFTError(token, args.tokenIds[i]);
194:                    }
195:                } else {
196:                    // If the call failed, ensure no ETH was spent and skip this NFT.
197:                    if (address(this).balance != balanceBefore) {
198:                        revert ContributionsSpentForFailedBuyError();
199:                    }
200:
201:                    continue;
202:                }
203:            }
204:
205:            totalEthUsed += args.callValues[i];
206:
207:            ++tokensBought;
208:            tokens[tokensBought - 1] = token;
209:            args.tokenIds[tokensBought - 1] = args.tokenIds[i];
210:        }
211:
212:        // This is to prevent this crowdfund from finalizing a loss if nothing
213:        // was attempted to be bought (ie. `tokenIds` is empty) or all NFTs were
214:        // bought for free.
215:        if (totalEthUsed == 0) revert NothingBoughtError();
216:
217:        // Check number of tokens bought is not less than the minimum.
218:        if (tokensBought < args.minTokensBought) {
219:            revert NotEnoughTokensBoughtError(tokensBought, args.minTokensBought);
220:        }
221:
222:        // Check total ETH used is not less than the minimum.
223:        if (totalEthUsed < args.minTotalEthUsed) {
224:            revert NotEnoughEthUsedError(totalEthUsed, args.minTotalEthUsed);
225:        }
226:
227:        assembly {
228:            // Update length of `tokens`
229:            mstore(tokens, tokensBought)
230:            // Update length of `tokenIds`
231:            mstore(mload(args), tokensBought)
232:        }
233:
234:        return	// @audit-issue
235:            _finalize(
236:                tokens,
237:                args.tokenIds,
238:                totalEthUsed,
239:                args.governanceOpts,
240:                args.proposalEngineOpts,
241:                // If `_assertIsHost()` succeeded, the governance opts were validated.
242:                true
243:            );
244:    }
```
*GitHub*: [234](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L234-L234)
```solidity
Path: ./contracts/party/PartyGovernance.sol

355:    function getVotingPowerAt(
356:        address voter,
357:        uint40 timestamp
358:    ) external view returns (uint96 votingPower) {
359:        return getVotingPowerAt(voter, timestamp, type(uint256).max);	// @audit-issue
360:    }

368:    function getVotingPowerAt(
369:        address voter,
370:        uint40 timestamp,
371:        uint256 snapIndex
372:    ) public view returns (uint96 votingPower) {
373:        VotingPowerSnapshot memory snap = _getVotingPowerSnapshotAt(voter, timestamp, snapIndex);
374:        return (snap.isDelegated ? 0 : snap.intrinsicVotingPower) + snap.delegatedVotingPower;	// @audit-issue
375:    }

423:    function findVotingPowerSnapshotIndex(
424:        address voter,
425:        uint40 timestamp
426:    ) public view returns (uint256 index) {
427:        VotingPowerSnapshot[] storage snaps = _votingPowerSnapshotsByVoter[voter];
428:
429:        // Derived from Open Zeppelin binary search
430:        // ref: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Checkpoints.sol#L39
431:        uint256 high = snaps.length;
432:        uint256 low = 0;
433:        while (low < high) {
434:            uint256 mid = (low + high) / 2;
435:            if (snaps[mid].timestamp > timestamp) {
436:                // Entry is too recent.
437:                high = mid;
438:            } else {
439:                // Entry is older. This is our best guess for now.
440:                low = mid + 1;
441:            }
442:        }
443:
444:        // Return `type(uint256).max` if no valid voting snapshots found.
445:        return high == 0 ? type(uint256).max : high - 1;	// @audit-issue
446:    }

487:    function distribute(
488:        uint256 amount,
489:        ITokenDistributor.TokenType tokenType,
490:        address token,
491:        uint256 tokenId
492:    ) external returns (ITokenDistributor.DistributionInfo memory distInfo) {
493:        _assertNotGloballyDisabled();
494:        // Ignore if the party is calling functions on itself, like with
495:        // `FractionalizeProposal` and `DistributionProposal`.
496:        if (msg.sender != address(this)) {
497:            // Must not require a vote to create a distribution, otherwise
498:            // distributions can only be created through a distribution
499:            // proposal.
500:            if (_getSharedProposalStorage().opts.distributionsRequireVote) {
501:                revert DistributionsRequireVoteError();
502:            }
503:            // Must be an active member.
504:            VotingPowerSnapshot memory snap = _getLastVotingPowerSnapshotForVoter(msg.sender);
505:            if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {
506:                revert NotAuthorized();
507:            }
508:        }
509:        // Prevent creating a distribution if the party has not started.
510:        if (_getSharedProposalStorage().governanceValues.totalVotingPower == 0) {
511:            revert PartyNotStartedError();
512:        }
513:        // Get the address of the token distributor.
514:        ITokenDistributor distributor = ITokenDistributor(
515:            _GLOBALS.getAddress(LibGlobals.GLOBAL_TOKEN_DISTRIBUTOR)
516:        );
517:        emit DistributionCreated(tokenType, token, tokenId);
518:        // Notify third-party platforms that the governance NFT metadata has
519:        // updated for all tokens.
520:        emit BatchMetadataUpdate(0, type(uint256).max);
521:        // Create a native token distribution.
522:        address payable feeRecipient_ = feeRecipient;
523:        uint16 feeBps_ = feeBps;
524:        if (tokenType == ITokenDistributor.TokenType.Native) {
525:            return	// @audit-issue
526:                distributor.createNativeDistribution{ value: amount }(
527:                    Party(payable(address(this))),
528:                    feeRecipient_,
529:                    feeBps_
530:                );
531:        }
532:        // Otherwise must be an ERC20 token distribution.
533:        assert(tokenType == ITokenDistributor.TokenType.Erc20);
534:        IERC20(token).compatTransfer(address(distributor), amount);
535:        return
536:            distributor.createErc20Distribution(
537:                IERC20(token),
538:                Party(payable(address(this))),
539:                feeRecipient_,
540:                feeBps_
541:            );
542:    }

487:    function distribute(
488:        uint256 amount,
489:        ITokenDistributor.TokenType tokenType,
490:        address token,
491:        uint256 tokenId
492:    ) external returns (ITokenDistributor.DistributionInfo memory distInfo) {
493:        _assertNotGloballyDisabled();
494:        // Ignore if the party is calling functions on itself, like with
495:        // `FractionalizeProposal` and `DistributionProposal`.
496:        if (msg.sender != address(this)) {
497:            // Must not require a vote to create a distribution, otherwise
498:            // distributions can only be created through a distribution
499:            // proposal.
500:            if (_getSharedProposalStorage().opts.distributionsRequireVote) {
501:                revert DistributionsRequireVoteError();
502:            }
503:            // Must be an active member.
504:            VotingPowerSnapshot memory snap = _getLastVotingPowerSnapshotForVoter(msg.sender);
505:            if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {
506:                revert NotAuthorized();
507:            }
508:        }
509:        // Prevent creating a distribution if the party has not started.
510:        if (_getSharedProposalStorage().governanceValues.totalVotingPower == 0) {
511:            revert PartyNotStartedError();
512:        }
513:        // Get the address of the token distributor.
514:        ITokenDistributor distributor = ITokenDistributor(
515:            _GLOBALS.getAddress(LibGlobals.GLOBAL_TOKEN_DISTRIBUTOR)
516:        );
517:        emit DistributionCreated(tokenType, token, tokenId);
518:        // Notify third-party platforms that the governance NFT metadata has
519:        // updated for all tokens.
520:        emit BatchMetadataUpdate(0, type(uint256).max);
521:        // Create a native token distribution.
522:        address payable feeRecipient_ = feeRecipient;
523:        uint16 feeBps_ = feeBps;
524:        if (tokenType == ITokenDistributor.TokenType.Native) {
525:            return
526:                distributor.createNativeDistribution{ value: amount }(
527:                    Party(payable(address(this))),
528:                    feeRecipient_,
529:                    feeBps_
530:                );
531:        }
532:        // Otherwise must be an ERC20 token distribution.
533:        assert(tokenType == ITokenDistributor.TokenType.Erc20);
534:        IERC20(token).compatTransfer(address(distributor), amount);
535:        return	// @audit-issue
536:            distributor.createErc20Distribution(
537:                IERC20(token),
538:                Party(payable(address(this))),
539:                feeRecipient_,
540:                feeBps_
541:            );
542:    }

595:    function accept(uint256 proposalId, uint256 snapIndex) public returns (uint256 totalVotes) {
596:        // Get the information about the proposal.
597:        ProposalState storage info = _proposalStateByProposalId[proposalId];
598:        ProposalStateValues memory values = info.values;
599:
600:        // Can only vote in certain proposal statuses.
601:        {
602:            ProposalStatus status = _getProposalStatus(values);
603:            // Allow voting even if the proposal is passed/ready so it can
604:            // potentially reach 100% consensus, which unlocks special
605:            // behaviors for certain proposal types.
606:            if (
607:                status != ProposalStatus.Voting &&
608:                status != ProposalStatus.Passed &&
609:                status != ProposalStatus.Ready
610:            ) {
611:                revert BadProposalStatusError(status);
612:            }
613:        }
614:
615:        // Prevent voting in the same block as the last rage quit timestamp.
616:        // This is to prevent an exploit where a member can rage quit to reduce
617:        // the total voting power of the party, then propose and vote in the
618:        // same block since `getVotingPowerAt()` uses `values.proposedTime - 1`.
619:        // This would allow them to use the voting power snapshot just before
620:        // their card was burned to vote, potentially passing a proposal that
621:        // would have otherwise not passed.
622:        if (lastRageQuitTimestamp == block.timestamp) {
623:            revert CannotRageQuitAndAcceptError();
624:        }
625:
626:        // Cannot vote twice.
627:        if (info.hasVoted[msg.sender]) {
628:            revert AlreadyVotedError(msg.sender);
629:        }
630:        // Mark the caller as having voted.
631:        info.hasVoted[msg.sender] = true;
632:
633:        // Increase the total votes that have been cast on this proposal.
634:        uint96 votingPower = getVotingPowerAt(msg.sender, values.proposedTime - 1, snapIndex);
635:        values.votes += votingPower;
636:        if (isHost[msg.sender]) {
637:            ++values.numHostsAccepted;
638:        }
639:        info.values = values;
640:        emit ProposalAccepted(proposalId, msg.sender, votingPower);
641:
642:        // Update the proposal status if it has reached the pass threshold.
643:        if (
644:            values.passedTime == 0 &&
645:            _areVotesPassing(
646:                values.votes,
647:                values.totalVotingPower,
648:                _getSharedProposalStorage().governanceValues.passThresholdBps
649:            )
650:        ) {
651:            info.values.passedTime = uint40(block.timestamp);
652:            emit ProposalPassed(proposalId);
653:            // Notify third-party platforms that the governance NFT metadata has
654:            // updated for all tokens.
655:            emit BatchMetadataUpdate(0, type(uint256).max);
656:        }
657:        return values.votes;	// @audit-issue
658:    }

860:    function _executeProposal(
861:        uint256 proposalId,
862:        Proposal memory proposal,
863:        IERC721[] memory preciousTokens,
864:        uint256[] memory preciousTokenIds,
865:        uint256 flags,
866:        bytes memory progressData,
867:        bytes memory extraData
868:    ) private returns (bool completed) {
869:        // Setup the arguments for the proposal execution engine.
870:        IProposalExecutionEngine.ExecuteProposalParams
871:            memory executeParams = IProposalExecutionEngine.ExecuteProposalParams({
872:                proposalId: proposalId,
873:                proposalData: proposal.proposalData,
874:                progressData: progressData,
875:                extraData: extraData,
876:                preciousTokens: preciousTokens,
877:                preciousTokenIds: preciousTokenIds,
878:                flags: flags
879:            });
880:        // Get the progress data returned after the proposal is executed.
881:        bytes memory nextProgressData;
882:        {
883:            // Execute the proposal.
884:            (bool success, bytes memory resultData) = address(
885:                _getSharedProposalStorage().engineImpl
886:            ).delegatecall(
887:                    abi.encodeCall(IProposalExecutionEngine.executeProposal, (executeParams))
888:                );
889:            if (!success) {
890:                resultData.rawRevert();
891:            }
892:            nextProgressData = abi.decode(resultData, (bytes));
893:        }
894:        emit ProposalExecuted(proposalId, msg.sender, nextProgressData);
895:        // Notify third-party platforms that the governance NFT metadata has
896:        // updated for all tokens.
897:        emit BatchMetadataUpdate(0, type(uint256).max);
898:        // If the returned progress data is empty, then the proposal completed
899:        // and it should not be executed again.
900:        return nextProgressData.length == 0;	// @audit-issue
901:    }

904:    function _getVotingPowerSnapshotAt(
905:        address voter,
906:        uint40 timestamp,
907:        uint256 hintIndex
908:    ) internal view returns (VotingPowerSnapshot memory snap) {
909:        VotingPowerSnapshot[] storage snaps = _votingPowerSnapshotsByVoter[voter];
910:        uint256 snapsLength = snaps.length;
911:        if (snapsLength != 0) {
912:            if (
913:                // Hint is within bounds.
914:                hintIndex < snapsLength &&
915:                // Snapshot is not too recent.
916:                snaps[hintIndex].timestamp <= timestamp &&
917:                // Snapshot is not too old.
918:                (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)
919:            ) {
920:                return snaps[hintIndex];	// @audit-issue
921:            }
922:
923:            // Hint was wrong, fallback to binary search to find snapshot.
924:            hintIndex = findVotingPowerSnapshotIndex(voter, timestamp);
925:            // Check that snapshot was found.
926:            if (hintIndex != type(uint256).max) {
927:                return snaps[hintIndex];
928:            }
929:        }
930:
931:        // No snapshot found.
932:        return snap;
933:    }

904:    function _getVotingPowerSnapshotAt(
905:        address voter,
906:        uint40 timestamp,
907:        uint256 hintIndex
908:    ) internal view returns (VotingPowerSnapshot memory snap) {
909:        VotingPowerSnapshot[] storage snaps = _votingPowerSnapshotsByVoter[voter];
910:        uint256 snapsLength = snaps.length;
911:        if (snapsLength != 0) {
912:            if (
913:                // Hint is within bounds.
914:                hintIndex < snapsLength &&
915:                // Snapshot is not too recent.
916:                snaps[hintIndex].timestamp <= timestamp &&
917:                // Snapshot is not too old.
918:                (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)
919:            ) {
920:                return snaps[hintIndex];
921:            }
922:
923:            // Hint was wrong, fallback to binary search to find snapshot.
924:            hintIndex = findVotingPowerSnapshotIndex(voter, timestamp);
925:            // Check that snapshot was found.
926:            if (hintIndex != type(uint256).max) {
927:                return snaps[hintIndex];
928:            }
929:        }
930:
931:        // No snapshot found.
932:        return snap;	// @audit-issue
933:    }

1064:    function _getProposalStatus(
1065:        ProposalStateValues memory pv
1066:    ) private view returns (ProposalStatus status) {
1067:        // Never proposed.
1068:        if (pv.proposedTime == 0) {
1069:            return ProposalStatus.Invalid;	// @audit-issue
1070:        }
1071:        // Executed at least once.
1072:        if (pv.executedTime != 0) {
1073:            if (pv.completedTime == 0) {
1074:                return ProposalStatus.InProgress;
1075:            }
1076:            // completedTime high bit will be set if cancelled.
1077:            if (pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT) {
1078:                return ProposalStatus.Cancelled;
1079:            }
1080:            return ProposalStatus.Complete;
1081:        }
1082:        // Vetoed.
1083:        if (pv.votes == type(uint96).max) {
1084:            return ProposalStatus.Defeated;
1085:        }
1086:        uint40 t = uint40(block.timestamp);
1087:        GovernanceValues memory gv = _getSharedProposalStorage().governanceValues;
1088:        if (pv.passedTime != 0) {
1089:            // Ready.
1090:            if (pv.passedTime + gv.executionDelay <= t) {
1091:                return ProposalStatus.Ready;
1092:            }
1093:            // If unanimous, we skip the execution delay.
1094:            if (_isUnanimousVotes(pv.votes, pv.totalVotingPower)) {
1095:                return ProposalStatus.Ready;
1096:            }
1097:            // If all hosts voted, skip execution delay
1098:            if (_hostsAccepted(pv.numHosts, pv.numHostsAccepted)) {
1099:                return ProposalStatus.Ready;
1100:            }
1101:            // Passed.
1102:            return ProposalStatus.Passed;
1103:        }
1104:        // Voting window expired.
1105:        if (pv.proposedTime + gv.voteDuration <= t) {
1106:            return ProposalStatus.Defeated;
1107:        }
1108:        return ProposalStatus.Voting;
1109:    }

1064:    function _getProposalStatus(
1065:        ProposalStateValues memory pv
1066:    ) private view returns (ProposalStatus status) {
1067:        // Never proposed.
1068:        if (pv.proposedTime == 0) {
1069:            return ProposalStatus.Invalid;
1070:        }
1071:        // Executed at least once.
1072:        if (pv.executedTime != 0) {
1073:            if (pv.completedTime == 0) {
1074:                return ProposalStatus.InProgress;	// @audit-issue
1075:            }
1076:            // completedTime high bit will be set if cancelled.
1077:            if (pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT) {
1078:                return ProposalStatus.Cancelled;
1079:            }
1080:            return ProposalStatus.Complete;
1081:        }
1082:        // Vetoed.
1083:        if (pv.votes == type(uint96).max) {
1084:            return ProposalStatus.Defeated;
1085:        }
1086:        uint40 t = uint40(block.timestamp);
1087:        GovernanceValues memory gv = _getSharedProposalStorage().governanceValues;
1088:        if (pv.passedTime != 0) {
1089:            // Ready.
1090:            if (pv.passedTime + gv.executionDelay <= t) {
1091:                return ProposalStatus.Ready;
1092:            }
1093:            // If unanimous, we skip the execution delay.
1094:            if (_isUnanimousVotes(pv.votes, pv.totalVotingPower)) {
1095:                return ProposalStatus.Ready;
1096:            }
1097:            // If all hosts voted, skip execution delay
1098:            if (_hostsAccepted(pv.numHosts, pv.numHostsAccepted)) {
1099:                return ProposalStatus.Ready;
1100:            }
1101:            // Passed.
1102:            return ProposalStatus.Passed;
1103:        }
1104:        // Voting window expired.
1105:        if (pv.proposedTime + gv.voteDuration <= t) {
1106:            return ProposalStatus.Defeated;
1107:        }
1108:        return ProposalStatus.Voting;
1109:    }

1064:    function _getProposalStatus(
1065:        ProposalStateValues memory pv
1066:    ) private view returns (ProposalStatus status) {
1067:        // Never proposed.
1068:        if (pv.proposedTime == 0) {
1069:            return ProposalStatus.Invalid;
1070:        }
1071:        // Executed at least once.
1072:        if (pv.executedTime != 0) {
1073:            if (pv.completedTime == 0) {
1074:                return ProposalStatus.InProgress;
1075:            }
1076:            // completedTime high bit will be set if cancelled.
1077:            if (pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT) {
1078:                return ProposalStatus.Cancelled;
1079:            }
1080:            return ProposalStatus.Complete;
1081:        }
1082:        // Vetoed.
1083:        if (pv.votes == type(uint96).max) {
1084:            return ProposalStatus.Defeated;	// @audit-issue
1085:        }
1086:        uint40 t = uint40(block.timestamp);
1087:        GovernanceValues memory gv = _getSharedProposalStorage().governanceValues;
1088:        if (pv.passedTime != 0) {
1089:            // Ready.
1090:            if (pv.passedTime + gv.executionDelay <= t) {
1091:                return ProposalStatus.Ready;
1092:            }
1093:            // If unanimous, we skip the execution delay.
1094:            if (_isUnanimousVotes(pv.votes, pv.totalVotingPower)) {
1095:                return ProposalStatus.Ready;
1096:            }
1097:            // If all hosts voted, skip execution delay
1098:            if (_hostsAccepted(pv.numHosts, pv.numHostsAccepted)) {
1099:                return ProposalStatus.Ready;
1100:            }
1101:            // Passed.
1102:            return ProposalStatus.Passed;
1103:        }
1104:        // Voting window expired.
1105:        if (pv.proposedTime + gv.voteDuration <= t) {
1106:            return ProposalStatus.Defeated;
1107:        }
1108:        return ProposalStatus.Voting;
1109:    }

1064:    function _getProposalStatus(
1065:        ProposalStateValues memory pv
1066:    ) private view returns (ProposalStatus status) {
1067:        // Never proposed.
1068:        if (pv.proposedTime == 0) {
1069:            return ProposalStatus.Invalid;
1070:        }
1071:        // Executed at least once.
1072:        if (pv.executedTime != 0) {
1073:            if (pv.completedTime == 0) {
1074:                return ProposalStatus.InProgress;
1075:            }
1076:            // completedTime high bit will be set if cancelled.
1077:            if (pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT) {
1078:                return ProposalStatus.Cancelled;
1079:            }
1080:            return ProposalStatus.Complete;
1081:        }
1082:        // Vetoed.
1083:        if (pv.votes == type(uint96).max) {
1084:            return ProposalStatus.Defeated;
1085:        }
1086:        uint40 t = uint40(block.timestamp);
1087:        GovernanceValues memory gv = _getSharedProposalStorage().governanceValues;
1088:        if (pv.passedTime != 0) {
1089:            // Ready.
1090:            if (pv.passedTime + gv.executionDelay <= t) {
1091:                return ProposalStatus.Ready;	// @audit-issue
1092:            }
1093:            // If unanimous, we skip the execution delay.
1094:            if (_isUnanimousVotes(pv.votes, pv.totalVotingPower)) {
1095:                return ProposalStatus.Ready;
1096:            }
1097:            // If all hosts voted, skip execution delay
1098:            if (_hostsAccepted(pv.numHosts, pv.numHostsAccepted)) {
1099:                return ProposalStatus.Ready;
1100:            }
1101:            // Passed.
1102:            return ProposalStatus.Passed;
1103:        }
1104:        // Voting window expired.
1105:        if (pv.proposedTime + gv.voteDuration <= t) {
1106:            return ProposalStatus.Defeated;
1107:        }
1108:        return ProposalStatus.Voting;
1109:    }

1064:    function _getProposalStatus(
1065:        ProposalStateValues memory pv
1066:    ) private view returns (ProposalStatus status) {
1067:        // Never proposed.
1068:        if (pv.proposedTime == 0) {
1069:            return ProposalStatus.Invalid;
1070:        }
1071:        // Executed at least once.
1072:        if (pv.executedTime != 0) {
1073:            if (pv.completedTime == 0) {
1074:                return ProposalStatus.InProgress;
1075:            }
1076:            // completedTime high bit will be set if cancelled.
1077:            if (pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT) {
1078:                return ProposalStatus.Cancelled;
1079:            }
1080:            return ProposalStatus.Complete;
1081:        }
1082:        // Vetoed.
1083:        if (pv.votes == type(uint96).max) {
1084:            return ProposalStatus.Defeated;
1085:        }
1086:        uint40 t = uint40(block.timestamp);
1087:        GovernanceValues memory gv = _getSharedProposalStorage().governanceValues;
1088:        if (pv.passedTime != 0) {
1089:            // Ready.
1090:            if (pv.passedTime + gv.executionDelay <= t) {
1091:                return ProposalStatus.Ready;
1092:            }
1093:            // If unanimous, we skip the execution delay.
1094:            if (_isUnanimousVotes(pv.votes, pv.totalVotingPower)) {
1095:                return ProposalStatus.Ready;
1096:            }
1097:            // If all hosts voted, skip execution delay
1098:            if (_hostsAccepted(pv.numHosts, pv.numHostsAccepted)) {
1099:                return ProposalStatus.Ready;
1100:            }
1101:            // Passed.
1102:            return ProposalStatus.Passed;
1103:        }
1104:        // Voting window expired.
1105:        if (pv.proposedTime + gv.voteDuration <= t) {
1106:            return ProposalStatus.Defeated;	// @audit-issue
1107:        }
1108:        return ProposalStatus.Voting;
1109:    }

1064:    function _getProposalStatus(
1065:        ProposalStateValues memory pv
1066:    ) private view returns (ProposalStatus status) {
1067:        // Never proposed.
1068:        if (pv.proposedTime == 0) {
1069:            return ProposalStatus.Invalid;
1070:        }
1071:        // Executed at least once.
1072:        if (pv.executedTime != 0) {
1073:            if (pv.completedTime == 0) {
1074:                return ProposalStatus.InProgress;
1075:            }
1076:            // completedTime high bit will be set if cancelled.
1077:            if (pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT) {
1078:                return ProposalStatus.Cancelled;
1079:            }
1080:            return ProposalStatus.Complete;
1081:        }
1082:        // Vetoed.
1083:        if (pv.votes == type(uint96).max) {
1084:            return ProposalStatus.Defeated;
1085:        }
1086:        uint40 t = uint40(block.timestamp);
1087:        GovernanceValues memory gv = _getSharedProposalStorage().governanceValues;
1088:        if (pv.passedTime != 0) {
1089:            // Ready.
1090:            if (pv.passedTime + gv.executionDelay <= t) {
1091:                return ProposalStatus.Ready;
1092:            }
1093:            // If unanimous, we skip the execution delay.
1094:            if (_isUnanimousVotes(pv.votes, pv.totalVotingPower)) {
1095:                return ProposalStatus.Ready;
1096:            }
1097:            // If all hosts voted, skip execution delay
1098:            if (_hostsAccepted(pv.numHosts, pv.numHostsAccepted)) {
1099:                return ProposalStatus.Ready;
1100:            }
1101:            // Passed.
1102:            return ProposalStatus.Passed;
1103:        }
1104:        // Voting window expired.
1105:        if (pv.proposedTime + gv.voteDuration <= t) {
1106:            return ProposalStatus.Defeated;
1107:        }
1108:        return ProposalStatus.Voting;	// @audit-issue
1109:    }
```
*GitHub*: [359](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L359-L359), [374](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L374-L374), [445](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L445-L445), [525](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L525-L525), [535](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L535-L535), [657](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L657-L657), [900](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L900-L900), [920](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L920-L920), [932](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L932-L932), [1069](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1069-L1069), [1074](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1074-L1074), [1084](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1084-L1084), [1091](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1091-L1091), [1106](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1106-L1106), [1108](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1108-L1108)
```solidity
Path: ./contracts/utils/LibRenderer.sol

78:    function generateColorHex(
79:        Color color,
80:        ColorType colorType
81:    ) external pure returns (string memory colorHex) {
82:        if (color == Color.DEFAULT) {
83:            if (colorType == ColorType.PRIMARY) {
84:                return "#A7B8CF";	// @audit-issue
85:            } else if (colorType == ColorType.SECONDARY) {
86:                return "#DCE5F0";
87:            } else if (colorType == ColorType.LIGHT) {
88:                return "#91A6C3";
89:            } else if (colorType == ColorType.DARK) {
90:                return "#50586D";
91:            }
92:        } else if (color == Color.GREEN) {
93:            if (colorType == ColorType.PRIMARY) {
94:                return "#10B173";
95:            } else if (colorType == ColorType.SECONDARY) {
96:                return "#93DCB7";
97:            } else if (colorType == ColorType.LIGHT) {
98:                return "#00A25A";
99:            } else if (colorType == ColorType.DARK) {
100:                return "#005E3B";
101:            }
102:        } else if (color == Color.CYAN) {
103:            if (colorType == ColorType.PRIMARY) {
104:                return "#00C1FA";
105:            } else if (colorType == ColorType.SECONDARY) {
106:                return "#B1EFFD";
107:            } else if (colorType == ColorType.LIGHT) {
108:                return "#00B4EA";
109:            } else if (colorType == ColorType.DARK) {
110:                return "#005669";
111:            }
112:        } else if (color == Color.BLUE) {
113:            if (colorType == ColorType.PRIMARY) {
114:                return "#2C78F3";
115:            } else if (colorType == ColorType.SECONDARY) {
116:                return "#B3D4FF";
117:            } else if (colorType == ColorType.LIGHT) {
118:                return "#0E70E0";
119:            } else if (colorType == ColorType.DARK) {
120:                return "#00286A";
121:            }
122:        } else if (color == Color.PURPLE) {
123:            if (colorType == ColorType.PRIMARY) {
124:                return "#9B45DF";
125:            } else if (colorType == ColorType.SECONDARY) {
126:                return "#D2ACF2";
127:            } else if (colorType == ColorType.LIGHT) {
128:                return "#832EC9";
129:            } else if (colorType == ColorType.DARK) {
130:                return "#47196B";
131:            }
132:        } else if (color == Color.PINK) {
133:            if (colorType == ColorType.PRIMARY) {
134:                return "#FF6BF3";
135:            } else if (colorType == ColorType.SECONDARY) {
136:                return "#FFC8FB";
137:            } else if (colorType == ColorType.LIGHT) {
138:                return "#E652E2";
139:            } else if (colorType == ColorType.DARK) {
140:                return "#911A96";
141:            }
142:        } else if (color == Color.ORANGE) {
143:            if (colorType == ColorType.PRIMARY) {
144:                return "#FF8946";
145:            } else if (colorType == ColorType.SECONDARY) {
146:                return "#FFE38B";
147:            } else if (colorType == ColorType.LIGHT) {
148:                return "#E47B2F";
149:            } else if (colorType == ColorType.DARK) {
150:                return "#732700";
151:            }
152:        } else if (color == Color.RED) {
153:            if (colorType == ColorType.PRIMARY) {
154:                return "#EC0000";
155:            } else if (colorType == ColorType.SECONDARY) {
156:                return "#FFA6A6";
157:            } else if (colorType == ColorType.LIGHT) {
158:                return "#D70000";
159:            } else if (colorType == ColorType.DARK) {
160:                return "#6F0000";
161:            }
162:        }
163:    }
```
*GitHub*: [84](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L84-L84)
```solidity
Path: ./contracts/utils/LibSafeERC721.sol

16:    function safeOwnerOf(IERC721 token, uint256 tokenId) internal view returns (address owner) {
17:        (bool s, bytes memory r) = address(token).staticcall(
18:            abi.encodeCall(token.ownerOf, (tokenId))
19:        );
20:
21:        if (!s || r.length < 32) {
22:            return address(0);	// @audit-issue
23:        }
24:
25:        return abi.decode(r, (address));
26:    }

16:    function safeOwnerOf(IERC721 token, uint256 tokenId) internal view returns (address owner) {
17:        (bool s, bytes memory r) = address(token).staticcall(
18:            abi.encodeCall(token.ownerOf, (tokenId))
19:        );
20:
21:        if (!s || r.length < 32) {
22:            return address(0);
23:        }
24:
25:        return abi.decode(r, (address));	// @audit-issue
26:    }
```
*GitHub*: [22](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeERC721.sol#L22-L22), [25](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeERC721.sol#L25-L25)
```solidity
Path: ./contracts/utils/PartyHelpers.sol

120:    function getRageQuitWithdrawAmounts(
121:        Party party,
122:        uint256[] calldata tokenIds,
123:        IERC20[] calldata withdrawTokens
124:    ) external view returns (uint256[] memory withdrawAmounts) {
125:        withdrawAmounts = new uint256[](withdrawTokens.length);
126:
127:        uint16 feeBps_ = party.feeBps();
128:        for (uint256 i; i < tokenIds.length; ++i) {
129:            uint256 tokenId = tokenIds[i];
130:            uint256 shareOfVotingPower = party.getVotingPowerShareOf(tokenId);
131:
132:            for (uint256 j; j < withdrawTokens.length; ++j) {
133:                // Calculate amount to withdraw.
134:                IERC20 token = withdrawTokens[j];
135:
136:                uint256 balance = address(token) == ETH_ADDRESS
137:                    ? address(party).balance
138:                    : token.balanceOf(address(party));
139:
140:                uint256 amount = (balance * shareOfVotingPower) / 1e18;
141:
142:                // Take fee from amount.
143:                uint256 fee = (amount * feeBps_) / 1e4;
144:
145:                // Sum up amount to withdraw.
146:                withdrawAmounts[j] += amount - fee;
147:            }
148:        }
149:
150:        return withdrawAmounts;	// @audit-issue
151:    }
```
*GitHub*: [150](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/PartyHelpers.sol#L150-L150)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

124:    function _executeListOnOpenseaAdvanced(
125:        IProposalExecutionEngine.ExecuteProposalParams memory params
126:    ) internal returns (bytes memory nextProgressData) {
127:        OpenseaAdvancedProposalData memory data = abi.decode(
128:            params.proposalData,
129:            (OpenseaAdvancedProposalData)
130:        );
131:
132:        return _executeAdvancedOpenseaProposal(params, data);	// @audit-issue
133:    }

135:    function _executeAdvancedOpenseaProposal(
136:        IProposalExecutionEngine.ExecuteProposalParams memory params,
137:        OpenseaAdvancedProposalData memory data
138:    ) internal override returns (bytes memory nextProgressData) {
139:        bool isUnanimous = params.flags & LibProposal.PROPOSAL_FLAG_UNANIMOUS ==
140:            LibProposal.PROPOSAL_FLAG_UNANIMOUS;
141:        // If there is no `progressData` passed in, we're on the first step,
142:        // otherwise parse the first word of the `progressData` as the current step.
143:        ListOnOpenseaStep step = params.progressData.length == 0
144:            ? ListOnOpenseaStep.None
145:            : abi.decode(params.progressData, (ListOnOpenseaStep));
146:        if (step == ListOnOpenseaStep.None) {
147:            // First time executing the proposal.
148:            if (
149:                !isUnanimous &&
150:                LibProposal.isTokenIdPrecious(
151:                    IERC721(data.token),
152:                    data.tokenId,
153:                    params.preciousTokens,
154:                    params.preciousTokenIds
155:                )
156:            ) {
157:                // Not a unanimous vote and the token is precious, so list on Zora
158:                // auction house first.
159:                uint40 zoraTimeout = uint40(
160:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_ZORA_AUCTION_TIMEOUT)
161:                );
162:                uint40 zoraDuration = uint40(
163:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_ZORA_AUCTION_DURATION)
164:                );
165:                if (zoraTimeout != 0) {
166:                    _createZoraAuction(
167:                        data.startPrice,
168:                        zoraTimeout,
169:                        zoraDuration,
170:                        data.token,
171:                        data.tokenId
172:                    );
173:                    // Return the next step and data required to execute that step.
174:                    return	// @audit-issue
175:                        abi.encode(
176:                            ListOnOpenseaStep.ListedOnZora,
177:                            ZoraProgressData({
178:                                minExpiry: (block.timestamp + zoraTimeout).safeCastUint256ToUint40()
179:                            })
180:                        );
181:                }
182:            }
183:            // Unanimous vote, not a precious, or no Zora duration.
184:            // Advance past the Zora auction phase by pretending we already
185:            // retrieved it from Zora.
186:            step = ListOnOpenseaStep.RetrievedFromZora;
187:        }
188:        if (step == ListOnOpenseaStep.ListedOnZora) {
189:            // The last time this proposal was executed, we listed it on Zora.
190:            // Now retrieve it from Zora.
191:            (, ZoraProgressData memory zpd) = abi.decode(
192:                params.progressData,
193:                (uint8, ZoraProgressData)
194:            );
195:            // Try to settle the Zora auction. This will revert if the auction
196:            // is still ongoing.
197:            ZoraAuctionStatus statusCode = _settleZoraAuction(
198:                zpd.minExpiry,
199:                data.token,
200:                data.tokenId
201:            );
202:            if (statusCode == ZoraAuctionStatus.Sold || statusCode == ZoraAuctionStatus.Cancelled) {
203:                // Auction sold or was cancelled. If it sold, there is nothing left to do.
204:                // If it was cancelled, we cannot safely proceed with the listing. Return
205:                // empty progress data to indicate there are no more steps to
206:                // execute.
207:                return "";
208:            }
209:            // The auction simply expired before anyone bid on it. We have the NFT
210:            // back now so move on to listing it on OpenSea immediately.
211:            step = ListOnOpenseaStep.RetrievedFromZora;
212:        }
213:        if (step == ListOnOpenseaStep.RetrievedFromZora) {
214:            // This step occurs if either:
215:            // 1) This is the first time this proposal is being executed and
216:            //    it is a unanimous vote or the NFT is not precious (guarded)
217:            //    so we intentionally skip the Zora listing step.
218:            // 2) The last time this proposal was executed, we settled an expired
219:            //    (no bids) Zora auction and can now proceed to the OpenSea
220:            //    listing step.
221:
222:            {
223:                // Clamp the order duration to the global minimum and maximum.
224:                uint40 minDuration = uint40(
225:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_MIN_ORDER_DURATION)
226:                );
227:                uint40 maxDuration = uint40(
228:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_MAX_ORDER_DURATION)
229:                );
230:                if (minDuration != 0 && data.duration < minDuration) {
231:                    data.duration = minDuration;
232:                } else if (maxDuration != 0 && data.duration > maxDuration) {
233:                    data.duration = maxDuration;
234:                }
235:            }
236:            uint256 expiry = block.timestamp + uint256(data.duration);
237:            (address conduit, bytes32 conduitKey) = _approveConduit(
238:                data.token,
239:                data.tokenId,
240:                data.tokenType
241:            );
242:            bytes32 orderHash = _listOnOpensea(data, conduitKey, expiry);
243:            return abi.encode(ListOnOpenseaStep.ListedOnOpenSea, orderHash, conduit, expiry);
244:        }
245:        assert(step == ListOnOpenseaStep.ListedOnOpenSea);
246:        // The last time this proposal was executed, we listed it on OpenSea.
247:        // Now try to settle the listing (either it has expired or been filled).
248:        (, OpenseaProgressData memory opd) = abi.decode(
249:            params.progressData,
250:            (uint8, OpenseaProgressData)
251:        );
252:        _cleanUpListing(
253:            opd.orderHash,
254:            opd.conduit,
255:            opd.expiry,
256:            data.token,
257:            data.tokenId,
258:            data.tokenType,
259:            data.startPrice,
260:            data.endPrice
261:        );
262:        // This is the last possible step so return empty progress data
263:        // to indicate there are no more steps to execute.
264:        return "";
265:    }

135:    function _executeAdvancedOpenseaProposal(
136:        IProposalExecutionEngine.ExecuteProposalParams memory params,
137:        OpenseaAdvancedProposalData memory data
138:    ) internal override returns (bytes memory nextProgressData) {
139:        bool isUnanimous = params.flags & LibProposal.PROPOSAL_FLAG_UNANIMOUS ==
140:            LibProposal.PROPOSAL_FLAG_UNANIMOUS;
141:        // If there is no `progressData` passed in, we're on the first step,
142:        // otherwise parse the first word of the `progressData` as the current step.
143:        ListOnOpenseaStep step = params.progressData.length == 0
144:            ? ListOnOpenseaStep.None
145:            : abi.decode(params.progressData, (ListOnOpenseaStep));
146:        if (step == ListOnOpenseaStep.None) {
147:            // First time executing the proposal.
148:            if (
149:                !isUnanimous &&
150:                LibProposal.isTokenIdPrecious(
151:                    IERC721(data.token),
152:                    data.tokenId,
153:                    params.preciousTokens,
154:                    params.preciousTokenIds
155:                )
156:            ) {
157:                // Not a unanimous vote and the token is precious, so list on Zora
158:                // auction house first.
159:                uint40 zoraTimeout = uint40(
160:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_ZORA_AUCTION_TIMEOUT)
161:                );
162:                uint40 zoraDuration = uint40(
163:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_ZORA_AUCTION_DURATION)
164:                );
165:                if (zoraTimeout != 0) {
166:                    _createZoraAuction(
167:                        data.startPrice,
168:                        zoraTimeout,
169:                        zoraDuration,
170:                        data.token,
171:                        data.tokenId
172:                    );
173:                    // Return the next step and data required to execute that step.
174:                    return
175:                        abi.encode(
176:                            ListOnOpenseaStep.ListedOnZora,
177:                            ZoraProgressData({
178:                                minExpiry: (block.timestamp + zoraTimeout).safeCastUint256ToUint40()
179:                            })
180:                        );
181:                }
182:            }
183:            // Unanimous vote, not a precious, or no Zora duration.
184:            // Advance past the Zora auction phase by pretending we already
185:            // retrieved it from Zora.
186:            step = ListOnOpenseaStep.RetrievedFromZora;
187:        }
188:        if (step == ListOnOpenseaStep.ListedOnZora) {
189:            // The last time this proposal was executed, we listed it on Zora.
190:            // Now retrieve it from Zora.
191:            (, ZoraProgressData memory zpd) = abi.decode(
192:                params.progressData,
193:                (uint8, ZoraProgressData)
194:            );
195:            // Try to settle the Zora auction. This will revert if the auction
196:            // is still ongoing.
197:            ZoraAuctionStatus statusCode = _settleZoraAuction(
198:                zpd.minExpiry,
199:                data.token,
200:                data.tokenId
201:            );
202:            if (statusCode == ZoraAuctionStatus.Sold || statusCode == ZoraAuctionStatus.Cancelled) {
203:                // Auction sold or was cancelled. If it sold, there is nothing left to do.
204:                // If it was cancelled, we cannot safely proceed with the listing. Return
205:                // empty progress data to indicate there are no more steps to
206:                // execute.
207:                return "";	// @audit-issue
208:            }
209:            // The auction simply expired before anyone bid on it. We have the NFT
210:            // back now so move on to listing it on OpenSea immediately.
211:            step = ListOnOpenseaStep.RetrievedFromZora;
212:        }
213:        if (step == ListOnOpenseaStep.RetrievedFromZora) {
214:            // This step occurs if either:
215:            // 1) This is the first time this proposal is being executed and
216:            //    it is a unanimous vote or the NFT is not precious (guarded)
217:            //    so we intentionally skip the Zora listing step.
218:            // 2) The last time this proposal was executed, we settled an expired
219:            //    (no bids) Zora auction and can now proceed to the OpenSea
220:            //    listing step.
221:
222:            {
223:                // Clamp the order duration to the global minimum and maximum.
224:                uint40 minDuration = uint40(
225:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_MIN_ORDER_DURATION)
226:                );
227:                uint40 maxDuration = uint40(
228:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_MAX_ORDER_DURATION)
229:                );
230:                if (minDuration != 0 && data.duration < minDuration) {
231:                    data.duration = minDuration;
232:                } else if (maxDuration != 0 && data.duration > maxDuration) {
233:                    data.duration = maxDuration;
234:                }
235:            }
236:            uint256 expiry = block.timestamp + uint256(data.duration);
237:            (address conduit, bytes32 conduitKey) = _approveConduit(
238:                data.token,
239:                data.tokenId,
240:                data.tokenType
241:            );
242:            bytes32 orderHash = _listOnOpensea(data, conduitKey, expiry);
243:            return abi.encode(ListOnOpenseaStep.ListedOnOpenSea, orderHash, conduit, expiry);
244:        }
245:        assert(step == ListOnOpenseaStep.ListedOnOpenSea);
246:        // The last time this proposal was executed, we listed it on OpenSea.
247:        // Now try to settle the listing (either it has expired or been filled).
248:        (, OpenseaProgressData memory opd) = abi.decode(
249:            params.progressData,
250:            (uint8, OpenseaProgressData)
251:        );
252:        _cleanUpListing(
253:            opd.orderHash,
254:            opd.conduit,
255:            opd.expiry,
256:            data.token,
257:            data.tokenId,
258:            data.tokenType,
259:            data.startPrice,
260:            data.endPrice
261:        );
262:        // This is the last possible step so return empty progress data
263:        // to indicate there are no more steps to execute.
264:        return "";
265:    }

135:    function _executeAdvancedOpenseaProposal(
136:        IProposalExecutionEngine.ExecuteProposalParams memory params,
137:        OpenseaAdvancedProposalData memory data
138:    ) internal override returns (bytes memory nextProgressData) {
139:        bool isUnanimous = params.flags & LibProposal.PROPOSAL_FLAG_UNANIMOUS ==
140:            LibProposal.PROPOSAL_FLAG_UNANIMOUS;
141:        // If there is no `progressData` passed in, we're on the first step,
142:        // otherwise parse the first word of the `progressData` as the current step.
143:        ListOnOpenseaStep step = params.progressData.length == 0
144:            ? ListOnOpenseaStep.None
145:            : abi.decode(params.progressData, (ListOnOpenseaStep));
146:        if (step == ListOnOpenseaStep.None) {
147:            // First time executing the proposal.
148:            if (
149:                !isUnanimous &&
150:                LibProposal.isTokenIdPrecious(
151:                    IERC721(data.token),
152:                    data.tokenId,
153:                    params.preciousTokens,
154:                    params.preciousTokenIds
155:                )
156:            ) {
157:                // Not a unanimous vote and the token is precious, so list on Zora
158:                // auction house first.
159:                uint40 zoraTimeout = uint40(
160:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_ZORA_AUCTION_TIMEOUT)
161:                );
162:                uint40 zoraDuration = uint40(
163:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_ZORA_AUCTION_DURATION)
164:                );
165:                if (zoraTimeout != 0) {
166:                    _createZoraAuction(
167:                        data.startPrice,
168:                        zoraTimeout,
169:                        zoraDuration,
170:                        data.token,
171:                        data.tokenId
172:                    );
173:                    // Return the next step and data required to execute that step.
174:                    return
175:                        abi.encode(
176:                            ListOnOpenseaStep.ListedOnZora,
177:                            ZoraProgressData({
178:                                minExpiry: (block.timestamp + zoraTimeout).safeCastUint256ToUint40()
179:                            })
180:                        );
181:                }
182:            }
183:            // Unanimous vote, not a precious, or no Zora duration.
184:            // Advance past the Zora auction phase by pretending we already
185:            // retrieved it from Zora.
186:            step = ListOnOpenseaStep.RetrievedFromZora;
187:        }
188:        if (step == ListOnOpenseaStep.ListedOnZora) {
189:            // The last time this proposal was executed, we listed it on Zora.
190:            // Now retrieve it from Zora.
191:            (, ZoraProgressData memory zpd) = abi.decode(
192:                params.progressData,
193:                (uint8, ZoraProgressData)
194:            );
195:            // Try to settle the Zora auction. This will revert if the auction
196:            // is still ongoing.
197:            ZoraAuctionStatus statusCode = _settleZoraAuction(
198:                zpd.minExpiry,
199:                data.token,
200:                data.tokenId
201:            );
202:            if (statusCode == ZoraAuctionStatus.Sold || statusCode == ZoraAuctionStatus.Cancelled) {
203:                // Auction sold or was cancelled. If it sold, there is nothing left to do.
204:                // If it was cancelled, we cannot safely proceed with the listing. Return
205:                // empty progress data to indicate there are no more steps to
206:                // execute.
207:                return "";
208:            }
209:            // The auction simply expired before anyone bid on it. We have the NFT
210:            // back now so move on to listing it on OpenSea immediately.
211:            step = ListOnOpenseaStep.RetrievedFromZora;
212:        }
213:        if (step == ListOnOpenseaStep.RetrievedFromZora) {
214:            // This step occurs if either:
215:            // 1) This is the first time this proposal is being executed and
216:            //    it is a unanimous vote or the NFT is not precious (guarded)
217:            //    so we intentionally skip the Zora listing step.
218:            // 2) The last time this proposal was executed, we settled an expired
219:            //    (no bids) Zora auction and can now proceed to the OpenSea
220:            //    listing step.
221:
222:            {
223:                // Clamp the order duration to the global minimum and maximum.
224:                uint40 minDuration = uint40(
225:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_MIN_ORDER_DURATION)
226:                );
227:                uint40 maxDuration = uint40(
228:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_MAX_ORDER_DURATION)
229:                );
230:                if (minDuration != 0 && data.duration < minDuration) {
231:                    data.duration = minDuration;
232:                } else if (maxDuration != 0 && data.duration > maxDuration) {
233:                    data.duration = maxDuration;
234:                }
235:            }
236:            uint256 expiry = block.timestamp + uint256(data.duration);
237:            (address conduit, bytes32 conduitKey) = _approveConduit(
238:                data.token,
239:                data.tokenId,
240:                data.tokenType
241:            );
242:            bytes32 orderHash = _listOnOpensea(data, conduitKey, expiry);
243:            return abi.encode(ListOnOpenseaStep.ListedOnOpenSea, orderHash, conduit, expiry);	// @audit-issue
244:        }
245:        assert(step == ListOnOpenseaStep.ListedOnOpenSea);
246:        // The last time this proposal was executed, we listed it on OpenSea.
247:        // Now try to settle the listing (either it has expired or been filled).
248:        (, OpenseaProgressData memory opd) = abi.decode(
249:            params.progressData,
250:            (uint8, OpenseaProgressData)
251:        );
252:        _cleanUpListing(
253:            opd.orderHash,
254:            opd.conduit,
255:            opd.expiry,
256:            data.token,
257:            data.tokenId,
258:            data.tokenType,
259:            data.startPrice,
260:            data.endPrice
261:        );
262:        // This is the last possible step so return empty progress data
263:        // to indicate there are no more steps to execute.
264:        return "";
265:    }

135:    function _executeAdvancedOpenseaProposal(
136:        IProposalExecutionEngine.ExecuteProposalParams memory params,
137:        OpenseaAdvancedProposalData memory data
138:    ) internal override returns (bytes memory nextProgressData) {
139:        bool isUnanimous = params.flags & LibProposal.PROPOSAL_FLAG_UNANIMOUS ==
140:            LibProposal.PROPOSAL_FLAG_UNANIMOUS;
141:        // If there is no `progressData` passed in, we're on the first step,
142:        // otherwise parse the first word of the `progressData` as the current step.
143:        ListOnOpenseaStep step = params.progressData.length == 0
144:            ? ListOnOpenseaStep.None
145:            : abi.decode(params.progressData, (ListOnOpenseaStep));
146:        if (step == ListOnOpenseaStep.None) {
147:            // First time executing the proposal.
148:            if (
149:                !isUnanimous &&
150:                LibProposal.isTokenIdPrecious(
151:                    IERC721(data.token),
152:                    data.tokenId,
153:                    params.preciousTokens,
154:                    params.preciousTokenIds
155:                )
156:            ) {
157:                // Not a unanimous vote and the token is precious, so list on Zora
158:                // auction house first.
159:                uint40 zoraTimeout = uint40(
160:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_ZORA_AUCTION_TIMEOUT)
161:                );
162:                uint40 zoraDuration = uint40(
163:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_ZORA_AUCTION_DURATION)
164:                );
165:                if (zoraTimeout != 0) {
166:                    _createZoraAuction(
167:                        data.startPrice,
168:                        zoraTimeout,
169:                        zoraDuration,
170:                        data.token,
171:                        data.tokenId
172:                    );
173:                    // Return the next step and data required to execute that step.
174:                    return
175:                        abi.encode(
176:                            ListOnOpenseaStep.ListedOnZora,
177:                            ZoraProgressData({
178:                                minExpiry: (block.timestamp + zoraTimeout).safeCastUint256ToUint40()
179:                            })
180:                        );
181:                }
182:            }
183:            // Unanimous vote, not a precious, or no Zora duration.
184:            // Advance past the Zora auction phase by pretending we already
185:            // retrieved it from Zora.
186:            step = ListOnOpenseaStep.RetrievedFromZora;
187:        }
188:        if (step == ListOnOpenseaStep.ListedOnZora) {
189:            // The last time this proposal was executed, we listed it on Zora.
190:            // Now retrieve it from Zora.
191:            (, ZoraProgressData memory zpd) = abi.decode(
192:                params.progressData,
193:                (uint8, ZoraProgressData)
194:            );
195:            // Try to settle the Zora auction. This will revert if the auction
196:            // is still ongoing.
197:            ZoraAuctionStatus statusCode = _settleZoraAuction(
198:                zpd.minExpiry,
199:                data.token,
200:                data.tokenId
201:            );
202:            if (statusCode == ZoraAuctionStatus.Sold || statusCode == ZoraAuctionStatus.Cancelled) {
203:                // Auction sold or was cancelled. If it sold, there is nothing left to do.
204:                // If it was cancelled, we cannot safely proceed with the listing. Return
205:                // empty progress data to indicate there are no more steps to
206:                // execute.
207:                return "";
208:            }
209:            // The auction simply expired before anyone bid on it. We have the NFT
210:            // back now so move on to listing it on OpenSea immediately.
211:            step = ListOnOpenseaStep.RetrievedFromZora;
212:        }
213:        if (step == ListOnOpenseaStep.RetrievedFromZora) {
214:            // This step occurs if either:
215:            // 1) This is the first time this proposal is being executed and
216:            //    it is a unanimous vote or the NFT is not precious (guarded)
217:            //    so we intentionally skip the Zora listing step.
218:            // 2) The last time this proposal was executed, we settled an expired
219:            //    (no bids) Zora auction and can now proceed to the OpenSea
220:            //    listing step.
221:
222:            {
223:                // Clamp the order duration to the global minimum and maximum.
224:                uint40 minDuration = uint40(
225:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_MIN_ORDER_DURATION)
226:                );
227:                uint40 maxDuration = uint40(
228:                    _GLOBALS.getUint256(LibGlobals.GLOBAL_OS_MAX_ORDER_DURATION)
229:                );
230:                if (minDuration != 0 && data.duration < minDuration) {
231:                    data.duration = minDuration;
232:                } else if (maxDuration != 0 && data.duration > maxDuration) {
233:                    data.duration = maxDuration;
234:                }
235:            }
236:            uint256 expiry = block.timestamp + uint256(data.duration);
237:            (address conduit, bytes32 conduitKey) = _approveConduit(
238:                data.token,
239:                data.tokenId,
240:                data.tokenType
241:            );
242:            bytes32 orderHash = _listOnOpensea(data, conduitKey, expiry);
243:            return abi.encode(ListOnOpenseaStep.ListedOnOpenSea, orderHash, conduit, expiry);
244:        }
245:        assert(step == ListOnOpenseaStep.ListedOnOpenSea);
246:        // The last time this proposal was executed, we listed it on OpenSea.
247:        // Now try to settle the listing (either it has expired or been filled).
248:        (, OpenseaProgressData memory opd) = abi.decode(
249:            params.progressData,
250:            (uint8, OpenseaProgressData)
251:        );
252:        _cleanUpListing(
253:            opd.orderHash,
254:            opd.conduit,
255:            opd.expiry,
256:            data.token,
257:            data.tokenId,
258:            data.tokenType,
259:            data.startPrice,
260:            data.endPrice
261:        );
262:        // This is the last possible step so return empty progress data
263:        // to indicate there are no more steps to execute.
264:        return "";	// @audit-issue
265:    }
```
*GitHub*: [132](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L132-L132), [174](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L174-L174), [207](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L207-L207), [243](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L243-L243), [264](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L264-L264)
```solidity
Path: ./contracts/proposals/OperatorProposal.sol

48:    function _executeOperation(
49:        IProposalExecutionEngine.ExecuteProposalParams memory params
50:    ) internal returns (bytes memory nextProgressData) {
51:        // Decode the proposal data.
52:        OperatorProposalData memory data = abi.decode(params.proposalData, (OperatorProposalData));
53:        (uint256 allowedExecutorsIndex, bytes memory executionData) = abi.decode(
54:            params.extraData,
55:            (uint256, bytes)
56:        );
57:
58:        // Check that the caller is an allowed executor.
59:        _assertCallerIsAllowedToExecute(msg.sender, data.allowedExecutors, allowedExecutorsIndex);
60:
61:        // Transfer assets to the operator contract to use on behalf of the party.
62:        uint256 ethToTransfer;
63:        for (uint256 i; i < data.assets.length; ++i) {
64:            AssetData memory asset = data.assets[i];
65:            if (asset.tokenType == OperatorTokenType.ETH) {
66:                ethToTransfer += asset.amount;
67:            } else if (asset.tokenType == OperatorTokenType.ERC20) {
68:                IERC20(asset.token).compatTransfer(address(data.operator), asset.amount);
69:            } else if (asset.tokenType == OperatorTokenType.ERC721) {
70:                IERC721(asset.token).transferFrom(
71:                    address(this),
72:                    address(data.operator),
73:                    asset.tokenId
74:                );
75:            } else if (asset.tokenType == OperatorTokenType.ERC1155) {
76:                IERC1155(asset.token).safeTransferFrom(
77:                    address(this),
78:                    address(data.operator),
79:                    asset.tokenId,
80:                    asset.amount,
81:                    ""
82:                );
83:            }
84:        }
85:
86:        // Execute the operation.
87:        data.operator.execute{ value: ethToTransfer }(data.operatorData, executionData, msg.sender);
88:
89:        // Nothing left to do.
90:        return "";	// @audit-issue
91:    }
```
*GitHub*: [90](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/OperatorProposal.sol#L90-L90)
```solidity
Path: ./contracts/proposals/ListOnOpenseaProposal.sol

32:    function _executeListOnOpensea(
33:        IProposalExecutionEngine.ExecuteProposalParams memory params
34:    ) internal returns (bytes memory nextProgressData) {
35:        OpenseaProposalData memory data = abi.decode(params.proposalData, (OpenseaProposalData));
36:
37:        return	// @audit-issue
38:            _executeAdvancedOpenseaProposal(
39:                params,
40:                ListOnOpenseaAdvancedProposal.OpenseaAdvancedProposalData({
41:                    startPrice: data.listPrice,
42:                    endPrice: data.listPrice,
43:                    duration: data.duration,
44:                    tokenType: ListOnOpenseaAdvancedProposal.TokenType.ERC721,
45:                    token: address(data.token),
46:                    tokenId: data.tokenId,
47:                    fees: data.fees,
48:                    feeRecipients: data.feeRecipients,
49:                    domainHashPrefix: data.domainHashPrefix
50:                })
51:            );
52:    }
```
*GitHub*: [37](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaProposal.sol#L37-L37)
```solidity
Path: ./contracts/proposals/AddAuthorityProposal.sol

17:    function _executeAddAuthority(
18:        IProposalExecutionEngine.ExecuteProposalParams memory params
19:    ) internal returns (bytes memory nextProgressData) {
20:        // Decode the proposal data.
21:        AddAuthorityProposalData memory data = abi.decode(
22:            params.proposalData,
23:            (AddAuthorityProposalData)
24:        );
25:
26:        address authority;
27:        if (data.callData.length == 0) {
28:            // Use the target as the authority if no call data is provided.
29:            authority = data.target;
30:        } else {
31:            // Call the target with the provided call data.
32:            (bool success, bytes memory response) = data.target.call(data.callData);
33:
34:            if (!success) {
35:                response.rawRevert();
36:            }
37:
38:            // Decode the response as the authority to add.
39:            (authority) = abi.decode(response, (address));
40:        }
41:
42:        // Add the crowdfund as an authority on the party.
43:        Party(payable(address(this))).addAuthority(authority);
44:
45:        // Nothing left to do.
46:        return "";	// @audit-issue
47:    }
```
*GitHub*: [46](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/AddAuthorityProposal.sol#L46-L46)
```solidity
Path: ./contracts/proposals/ArbitraryCallsProposal.sol

52:    function _executeArbitraryCalls(
53:        IProposalExecutionEngine.ExecuteProposalParams memory params,
54:        bool allowArbCallsToSpendPartyEth
55:    ) internal returns (bytes memory nextProgressData) {
56:        // Get the calls to execute.
57:        ArbitraryCall[] memory calls = abi.decode(params.proposalData, (ArbitraryCall[]));
58:        // Check whether the proposal was unanimously passed.
59:        bool isUnanimous = params.flags & LibProposal.PROPOSAL_FLAG_UNANIMOUS ==
60:            LibProposal.PROPOSAL_FLAG_UNANIMOUS;
61:        // If not unanimous, keep track of which preciouses we had before the calls
62:        // so we can check that we still have them later.
63:        bool[] memory hadPreciouses = new bool[](params.preciousTokenIds.length);
64:        if (!isUnanimous) {
65:            for (uint256 i; i < hadPreciouses.length; ++i) {
66:                hadPreciouses[i] = _getHasPrecious(
67:                    params.preciousTokens[i],
68:                    params.preciousTokenIds[i]
69:                );
70:            }
71:        }
72:        // If we're not allowing arbitrary calls to spend the Party's ETH, only
73:        // allow forwarded ETH attached to the call to be spent.
74:        uint256 ethAvailable = allowArbCallsToSpendPartyEth ? address(this).balance : msg.value;
75:        for (uint256 i; i < calls.length; ++i) {
76:            // Execute an arbitrary call.
77:            _executeSingleArbitraryCall(
78:                i,
79:                calls,
80:                params.preciousTokens,
81:                params.preciousTokenIds,
82:                isUnanimous,
83:                ethAvailable
84:            );
85:            // Update the amount of ETH available for the subsequent calls.
86:            ethAvailable = allowArbCallsToSpendPartyEth
87:                ? address(this).balance
88:                : ethAvailable - calls[i].value;
89:            emit ArbitraryCallExecuted(params.proposalId, i, calls.length);
90:        }
91:        // If not a unanimous vote and we had a precious beforehand,
92:        // ensure that we still have it now.
93:        if (!isUnanimous) {
94:            for (uint256 i; i < hadPreciouses.length; ++i) {
95:                if (hadPreciouses[i]) {
96:                    if (!_getHasPrecious(params.preciousTokens[i], params.preciousTokenIds[i])) {
97:                        revert PreciousLostError(
98:                            params.preciousTokens[i],
99:                            params.preciousTokenIds[i]
100:                        );
101:                    }
102:                }
103:            }
104:        }
105:        // Refund leftover ETH attached to the call if none was spent from the
106:        // Party's balance.
107:        if (!allowArbCallsToSpendPartyEth && ethAvailable > 0) {
108:            payable(msg.sender).transferEth(ethAvailable);
109:        }
110:        // No next step, so no progressData.
111:        return "";	// @audit-issue
112:    }

159:    function _isCallAllowed(
160:        ArbitraryCall memory call,
161:        bool isUnanimous,
162:        uint256 callIndex,
163:        uint256 callsCount,
164:        IERC721[] memory preciousTokens,
165:        uint256[] memory preciousTokenIds
166:    ) private view returns (bool isAllowed) {
167:        // Cannot call ourselves.
168:        if (call.target == address(this)) {
169:            return false;	// @audit-issue
170:        }
171:        if (call.data.length >= 4) {
172:            // Get the function selector of the call (first 4 bytes of calldata).
173:            bytes4 selector;
174:            {
175:                bytes memory callData = call.data;
176:                assembly {
177:                    selector := and(
178:                        mload(add(callData, 32)),
179:                        0xffffffff00000000000000000000000000000000000000000000000000000000
180:                    )
181:                }
182:            }
183:            // Non-unanimous proposals restrict what ways some functions can be
184:            // called on a precious token.
185:            if (!isUnanimous) {
186:                // Cannot call `approve()` or `setApprovalForAll()` on the precious
187:                // unless it's to revoke approvals.
188:                if (selector == IERC721.approve.selector) {
189:                    // Can only call `approve()` on the precious if the operator is null.
190:                    (address op, uint256 tokenId) = _decodeApproveCallDataArgs(call.data);
191:                    if (op != address(0)) {
192:                        return
193:                            !LibProposal.isTokenIdPrecious(
194:                                IERC721(call.target),
195:                                tokenId,
196:                                preciousTokens,
197:                                preciousTokenIds
198:                            );
199:                    }
200:                    // Can only call `setApprovalForAll()` on the precious if
201:                    // toggling off.
202:                } else if (selector == IERC721.setApprovalForAll.selector) {
203:                    (, bool isApproved) = _decodeSetApprovalForAllCallDataArgs(call.data);
204:                    if (isApproved) {
205:                        return !LibProposal.isTokenPrecious(IERC721(call.target), preciousTokens);
206:                    }
207:                    // Can only call cancelAuction on the zora AH if it's the last call
208:                    // in the sequence.
209:                } else if (selector == IReserveAuctionCoreEth.cancelAuction.selector) {
210:                    if (call.target == address(_ZORA)) {
211:                        return callIndex + 1 == callsCount;
212:                    }
213:                }
214:            }
215:            // Can never call receive hooks on any target.
216:            if (
217:                selector == IERC721Receiver.onERC721Received.selector ||
218:                selector == ERC1155TokenReceiverBase.onERC1155Received.selector ||
219:                selector == ERC1155TokenReceiverBase.onERC1155BatchReceived.selector
220:            ) {
221:                return false;
222:            }
223:            // Disallow calling `validate()` on Seaport if there are preciouses.
224:            if (selector == IOpenseaExchange.validate.selector && preciousTokens.length != 0) {
225:                return false;
226:            }
227:        }
228:        // All other calls are allowed.
229:        return true;
230:    }

159:    function _isCallAllowed(
160:        ArbitraryCall memory call,
161:        bool isUnanimous,
162:        uint256 callIndex,
163:        uint256 callsCount,
164:        IERC721[] memory preciousTokens,
165:        uint256[] memory preciousTokenIds
166:    ) private view returns (bool isAllowed) {
167:        // Cannot call ourselves.
168:        if (call.target == address(this)) {
169:            return false;
170:        }
171:        if (call.data.length >= 4) {
172:            // Get the function selector of the call (first 4 bytes of calldata).
173:            bytes4 selector;
174:            {
175:                bytes memory callData = call.data;
176:                assembly {
177:                    selector := and(
178:                        mload(add(callData, 32)),
179:                        0xffffffff00000000000000000000000000000000000000000000000000000000
180:                    )
181:                }
182:            }
183:            // Non-unanimous proposals restrict what ways some functions can be
184:            // called on a precious token.
185:            if (!isUnanimous) {
186:                // Cannot call `approve()` or `setApprovalForAll()` on the precious
187:                // unless it's to revoke approvals.
188:                if (selector == IERC721.approve.selector) {
189:                    // Can only call `approve()` on the precious if the operator is null.
190:                    (address op, uint256 tokenId) = _decodeApproveCallDataArgs(call.data);
191:                    if (op != address(0)) {
192:                        return	// @audit-issue
193:                            !LibProposal.isTokenIdPrecious(
194:                                IERC721(call.target),
195:                                tokenId,
196:                                preciousTokens,
197:                                preciousTokenIds
198:                            );
199:                    }
200:                    // Can only call `setApprovalForAll()` on the precious if
201:                    // toggling off.
202:                } else if (selector == IERC721.setApprovalForAll.selector) {
203:                    (, bool isApproved) = _decodeSetApprovalForAllCallDataArgs(call.data);
204:                    if (isApproved) {
205:                        return !LibProposal.isTokenPrecious(IERC721(call.target), preciousTokens);
206:                    }
207:                    // Can only call cancelAuction on the zora AH if it's the last call
208:                    // in the sequence.
209:                } else if (selector == IReserveAuctionCoreEth.cancelAuction.selector) {
210:                    if (call.target == address(_ZORA)) {
211:                        return callIndex + 1 == callsCount;
212:                    }
213:                }
214:            }
215:            // Can never call receive hooks on any target.
216:            if (
217:                selector == IERC721Receiver.onERC721Received.selector ||
218:                selector == ERC1155TokenReceiverBase.onERC1155Received.selector ||
219:                selector == ERC1155TokenReceiverBase.onERC1155BatchReceived.selector
220:            ) {
221:                return false;
222:            }
223:            // Disallow calling `validate()` on Seaport if there are preciouses.
224:            if (selector == IOpenseaExchange.validate.selector && preciousTokens.length != 0) {
225:                return false;
226:            }
227:        }
228:        // All other calls are allowed.
229:        return true;
230:    }

159:    function _isCallAllowed(
160:        ArbitraryCall memory call,
161:        bool isUnanimous,
162:        uint256 callIndex,
163:        uint256 callsCount,
164:        IERC721[] memory preciousTokens,
165:        uint256[] memory preciousTokenIds
166:    ) private view returns (bool isAllowed) {
167:        // Cannot call ourselves.
168:        if (call.target == address(this)) {
169:            return false;
170:        }
171:        if (call.data.length >= 4) {
172:            // Get the function selector of the call (first 4 bytes of calldata).
173:            bytes4 selector;
174:            {
175:                bytes memory callData = call.data;
176:                assembly {
177:                    selector := and(
178:                        mload(add(callData, 32)),
179:                        0xffffffff00000000000000000000000000000000000000000000000000000000
180:                    )
181:                }
182:            }
183:            // Non-unanimous proposals restrict what ways some functions can be
184:            // called on a precious token.
185:            if (!isUnanimous) {
186:                // Cannot call `approve()` or `setApprovalForAll()` on the precious
187:                // unless it's to revoke approvals.
188:                if (selector == IERC721.approve.selector) {
189:                    // Can only call `approve()` on the precious if the operator is null.
190:                    (address op, uint256 tokenId) = _decodeApproveCallDataArgs(call.data);
191:                    if (op != address(0)) {
192:                        return
193:                            !LibProposal.isTokenIdPrecious(
194:                                IERC721(call.target),
195:                                tokenId,
196:                                preciousTokens,
197:                                preciousTokenIds
198:                            );
199:                    }
200:                    // Can only call `setApprovalForAll()` on the precious if
201:                    // toggling off.
202:                } else if (selector == IERC721.setApprovalForAll.selector) {
203:                    (, bool isApproved) = _decodeSetApprovalForAllCallDataArgs(call.data);
204:                    if (isApproved) {
205:                        return !LibProposal.isTokenPrecious(IERC721(call.target), preciousTokens);
206:                    }
207:                    // Can only call cancelAuction on the zora AH if it's the last call
208:                    // in the sequence.
209:                } else if (selector == IReserveAuctionCoreEth.cancelAuction.selector) {
210:                    if (call.target == address(_ZORA)) {
211:                        return callIndex + 1 == callsCount;
212:                    }
213:                }
214:            }
215:            // Can never call receive hooks on any target.
216:            if (
217:                selector == IERC721Receiver.onERC721Received.selector ||
218:                selector == ERC1155TokenReceiverBase.onERC1155Received.selector ||
219:                selector == ERC1155TokenReceiverBase.onERC1155BatchReceived.selector
220:            ) {
221:                return false;
222:            }
223:            // Disallow calling `validate()` on Seaport if there are preciouses.
224:            if (selector == IOpenseaExchange.validate.selector && preciousTokens.length != 0) {
225:                return false;
226:            }
227:        }
228:        // All other calls are allowed.
229:        return true;	// @audit-issue
230:    }
```
*GitHub*: [111](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L111-L111), [169](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L169-L169), [192](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L192-L192), [229](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L229-L229)
```solidity
Path: ./contracts/proposals/FractionalizeProposal.sol

36:    function _executeFractionalize(
37:        IProposalExecutionEngine.ExecuteProposalParams memory params
38:    ) internal returns (bytes memory nextProgressData) {
39:        // Decode the proposal data.
40:        FractionalizeProposalData memory data = abi.decode(
41:            params.proposalData,
42:            (FractionalizeProposalData)
43:        );
44:        // The supply of fractional vault ERC20 tokens will be equal to the total
45:        // voting power of the party.
46:        uint256 supply = PartyGovernance(address(this)).getGovernanceValues().totalVotingPower;
47:        // Create a vault around the NFT.
48:        data.token.approve(address(VAULT_FACTORY), data.tokenId);
49:        uint256 vaultId = VAULT_FACTORY.mint(
50:            IERC721(address(this)).name(),
51:            IERC721(address(this)).symbol(),
52:            data.token,
53:            data.tokenId,
54:            supply,
55:            // Since we are distributing the entire supply immediately after
56:            // fractionalizing, in practice setting an initial reserve price
57:            // does not do anything because it will get reset to 0 after the
58:            // distribution is created.
59:            0,
60:            0
61:        );
62:        // Get the vault we just created.
63:        IFractionalV1Vault vault = VAULT_FACTORY.vaults(vaultId);
64:        // Check that we now hold the correct amount of fractional tokens.
65:        // Should always succeed.
66:        assert(vault.balanceOf(address(this)) == supply);
67:        // Remove ourselves as curator. Set to `address(1)` to avoid issues with
68:        // reverting when minting to `address(0)`.
69:        vault.updateCurator(address(1));
70:        emit FractionalV1VaultCreated(data.token, data.tokenId, vaultId, vault);
71:        // Create distribution for fractional tokens for party.
72:        PartyGovernance(address(this)).distribute(
73:            supply,
74:            ITokenDistributor.TokenType.Erc20,
75:            address(vault),
76:            vaultId
77:        );
78:        // Nothing left to do.
79:        return "";	// @audit-issue
80:    }
```
*GitHub*: [79](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/FractionalizeProposal.sol#L79-L79)
```solidity
Path: ./contracts/proposals/DistributeProposal.sol

17:    function _executeDistribute(
18:        IProposalExecutionEngine.ExecuteProposalParams memory params
19:    ) internal returns (bytes memory nextProgressData) {
20:        // Decode the proposal data.
21:        DistributeProposalData memory data = abi.decode(
22:            params.proposalData,
23:            (DistributeProposalData)
24:        );
25:
26:        // Create a distribution.
27:        PartyGovernance(address(this)).distribute(
28:            data.amount,
29:            data.tokenType,
30:            data.token,
31:            data.tokenId
32:        );
33:
34:        // Nothing left to do.
35:        return "";	// @audit-issue
36:    }
```
*GitHub*: [35](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/DistributeProposal.sol#L35-L35)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

396:    function _getBalanceId(
397:        TokenType tokenType,
398:        address token
399:    ) private pure returns (bytes32 balanceId) {
400:        if (tokenType == TokenType.Native) {
401:            return bytes32(uint256(uint160(NATIVE_TOKEN_ADDRESS)));	// @audit-issue
402:        }
403:        assert(tokenType == TokenType.Erc20);
404:        return bytes32(uint256(uint160(token)));
405:    }

396:    function _getBalanceId(
397:        TokenType tokenType,
398:        address token
399:    ) private pure returns (bytes32 balanceId) {
400:        if (tokenType == TokenType.Native) {
401:            return bytes32(uint256(uint160(NATIVE_TOKEN_ADDRESS)));
402:        }
403:        assert(tokenType == TokenType.Erc20);
404:        return bytes32(uint256(uint160(token)));	// @audit-issue
405:    }
```
*GitHub*: [401](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L401-L401), [404](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L404-L404)
</details>



### [N-06] Unused `error` definition
### Severity
* Impact: Low
* Likelihood: Low
### Description

Note that there may be cases where an error superficially appears to be used, but this is only because there are multiple definitions of the error in different files. In such cases, the error definition should be moved into a separate file. The instances below are the unused definitions.


### Number Of Instances Found
5

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

91:    error NoContributionsError();	// @audit-issue

92:    error AuctionNotExpiredError();	// @audit-issue
```
*GitHub*: [91](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L91-L91), [92](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L92-L92)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfundBase.sol

62:    error NoContributionsError();	// @audit-issue
```
*GitHub*: [62](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L62-L62)
```solidity
Path: ./contracts/proposals/OperatorProposal.sol

46:    error NotEnoughEthError(uint256 operatorValue, uint256 ethAvailable);	// @audit-issue
```
*GitHub*: [46](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/OperatorProposal.sol#L46-L46)
```solidity
Path: ./contracts/proposals/vendor/IOpenseaExchange.sol

5:    error InvalidTime(uint256 startTime, uint256 endTime);	// @audit-issue
```
*GitHub*: [5](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/vendor/IOpenseaExchange.sol#L5-L5)
</details>



### [N-07] Use `delete` instead of assigning values to `false`
### Severity
* Impact: Low
* Likelihood: Low
### Description

The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.


### Number Of Instances Found
1

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/party/PartyGovernance.sol

470:        isHost[msg.sender] = false;	// @audit-issue
```
*GitHub*: [470](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L470-L470)
</details>



### [N-08] Consider using `delete` rather than assigning zero to clear values
### Severity
* Impact: Low
* Likelihood: Low
### Description

The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

### Number Of Instances Found
9

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

603:            splitBps_ = 0;	// @audit-issue
```
*GitHub*: [603](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L603-L603)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfund.sol

65:            lastBid = 0;	// @audit-issue
```
*GitHub*: [65](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfund.sol#L65-L65)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfundBase.sol

158:            settledPrice = 0;	// @audit-issue
```
*GitHub*: [158](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L158-L158)
```solidity
Path: ./contracts/crowdfund/RollingAuctionCrowdfund.sol

143:            lastBid = 0;	// @audit-issue

189:            lastBid = 0;	// @audit-issue
```
*GitHub*: [143](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/RollingAuctionCrowdfund.sol#L143-L143), [189](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/RollingAuctionCrowdfund.sol#L189-L189)
```solidity
Path: ./contracts/party/PartyGovernance.sol

758:            proposalState.values.completedTime = 0;	// @audit-issue
```
*GitHub*: [758](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L758-L758)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

305:        cons.identifierOrCriteria = 0;	// @audit-issue

313:            cons.identifierOrCriteria = 0;	// @audit-issue

381:        orderParams.totalOriginalConsiderationItems = 0;	// @audit-issue
```
*GitHub*: [305](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L305-L305), [313](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L313-L313), [381](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L381-L381)
</details>



### [N-09] Too long functions should be refactored
### Severity
* Impact: Low
* Likelihood: Low
### Description

Functions with too many lines are difficult to understand. It is recommended to refactor complex functions into multiple shorter and easier to understand functions.


### Number Of Instances Found
25

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/InitialETHCrowdfund.sol

372:    function _createParty(	// @audit-issue 63 lines
```
*GitHub*: [372](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L372-L372)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

483:    function _createParty(	// @audit-issue 51 lines

631:    function _contribute(	// @audit-issue 75 lines

708:    function _burn(address payable contributor, CrowdfundLifecycle lc, Party party_) private {	// @audit-issue 55 lines
```
*GitHub*: [483](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L483-L483), [631](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L631-L631), [708](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L708-L708)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

228:    function _bid(uint96 amount) private onlyDelegateCall {	// @audit-issue 54 lines
```
*GitHub*: [228](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L228-L228)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfund.sol

122:    function buy(	// @audit-issue 57 lines
```
*GitHub*: [122](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfund.sol#L122-L122)
```solidity
Path: ./contracts/crowdfund/CollectionBuyCrowdfund.sol

113:    function buy(	// @audit-issue 50 lines
```
*GitHub*: [113](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBuyCrowdfund.sol#L113-L113)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

196:    function _processContribution(	// @audit-issue 77 lines
```
*GitHub*: [196](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L196-L196)
```solidity
Path: ./contracts/crowdfund/RollingAuctionCrowdfund.sol

105:    function finalizeOrRollOver(	// @audit-issue 91 lines
```
*GitHub*: [105](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/RollingAuctionCrowdfund.sol#L105-L105)
```solidity
Path: ./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol

134:    function batchBuy(BatchBuyArgs memory args) external onlyDelegateCall returns (Party party_) {	// @audit-issue 110 lines
```
*GitHub*: [134](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L134-L134)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

344:    function rageQuit(	// @audit-issue 104 lines
```
*GitHub*: [344](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L344-L344)
```solidity
Path: ./contracts/party/PartyGovernance.sol

487:    function distribute(	// @audit-issue 55 lines

595:    function accept(uint256 proposalId, uint256 snapIndex) public returns (uint256 totalVotes) {	// @audit-issue 63 lines

706:    function execute(	// @audit-issue 54 lines

771:    function cancel(uint256 proposalId, Proposal calldata proposal) external {	// @audit-issue 63 lines
```
*GitHub*: [487](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L487-L487), [595](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L595-L595), [706](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L706-L706), [771](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L771-L771)
```solidity
Path: ./contracts/utils/LibRenderer.sol

78:    function generateColorHex(	// @audit-issue 85 lines

165:    function getCollectionImageAndBanner(	// @audit-issue 60 lines
```
*GitHub*: [78](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L78-L78), [165](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L165-L165)
```solidity
Path: ./contracts/utils/vendor/Base64.sol

13:    function encode(bytes memory data) internal pure returns (string memory) {	// @audit-issue 50 lines
```
*GitHub*: [13](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Base64.sol#L13-L13)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

135:    function _executeAdvancedOpenseaProposal(	// @audit-issue 130 lines

267:    function _listOnOpensea(	// @audit-issue 79 lines
```
*GitHub*: [135](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L135-L135), [267](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L267-L267)
```solidity
Path: ./contracts/proposals/ArbitraryCallsProposal.sol

52:    function _executeArbitraryCalls(	// @audit-issue 60 lines

159:    function _isCallAllowed(	// @audit-issue 71 lines
```
*GitHub*: [52](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L52-L52), [159](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L159-L159)
```solidity
Path: ./contracts/operators/ERC20SwapOperator.sol

102:    function execute(	// @audit-issue 107 lines
```
*GitHub*: [102](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L102-L102)
```solidity
Path: ./contracts/operators/CollectionBatchBuyOperator.sol

85:    function execute(	// @audit-issue 132 lines
```
*GitHub*: [85](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/CollectionBatchBuyOperator.sol#L85-L85)
```solidity
Path: ./contracts/signature-validators/OffChainSignatureValidator.sol

28:    function isValidSignature(bytes32 hash, bytes memory signature) external view returns (bytes4) {	// @audit-issue 52 lines
```
*GitHub*: [28](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L28-L28)
</details>



### [N-10] Names of constants should use the UPPER_CASE_WITH_UNDERSCORES style
### Severity
* Impact: Low
* Likelihood: Low
### Description

It is recommended by the [Solidity Style Guide](https://docs.soliditylang.org/en/latest/style-guide.html)

### Number Of Instances Found
1

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/party/PartyGovernance.sol

186:    uint256 private constant UINT40_HIGH_BIT = 1 << 39;	// @audit-issue name should be: U_I_N_T_40_H_I_G_H_B_I_T
```
*GitHub*: [186](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L186-L186)
</details>



### [N-11] Variable names for `immutable`s should use CONSTANT_CASE
### Severity
* Impact: Low
* Likelihood: Low
### Description

For `immutable` variable names, each word should use all capital letters, with underscores separating each word (CONSTANT_CASE)

### Number Of Instances Found
4

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/market-wrapper/NounsMarketWrapper.sol

19:    INounsAuctionHouse public immutable market;	// @audit-issue name should be: MARKET
```
*GitHub*: [19](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/NounsMarketWrapper.sol#L19-L19)
```solidity
Path: ./contracts/market-wrapper/KoansMarketWrapper.sol

20:    IKoansAuctionHouse public immutable market;	// @audit-issue name should be: MARKET
```
*GitHub*: [20](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/KoansMarketWrapper.sol#L20-L20)
```solidity
Path: ./contracts/utils/Implementation.sol

12:    address public immutable implementation;	// @audit-issue name should be: IMPLEMENTATION
```
*GitHub*: [12](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/Implementation.sol#L12-L12)
```solidity
Path: ./contracts/vendor/solmate/ERC20.sol

23:    uint8 public immutable decimals;	// @audit-issue name should be: DECIMALS
```
*GitHub*: [23](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC20.sol#L23-L23)
</details>



### [N-12] Expressions for `constant` values should use `immutable` rather than `constant`
### Severity
* Impact: Low
* Likelihood: Low
### Description

While it does not save gas for some simple binary expressions because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constant`s should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the `constructor`.

### Number Of Instances Found
1

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/operators/ERC20SwapOperator.sol

61:    IERC20 private constant ETH_TOKEN_ADDRESS = IERC20(0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE);	// @audit-issue
```
*GitHub*: [61](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L61-L61)
</details>



### [N-13]  Use `bytes.concat()` on `bytes` instead of `abi.encodePacked()` for clearer semantic meaning
### Severity
* Impact: Low
* Likelihood: Low
### Description

Starting with version 0.8.4, Solidity has the `bytes.concat()` function, which allows one to concatenate a list of bytes/strings, without extra padding. Using this function rather than `abi.encodePacked()` makes the intended operation more clear, leading to less reviewer confusion.

### Number Of Instances Found
3

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/RollingAuctionCrowdfund.sol

165:                            abi.encodePacked(bytes32(0), args.nextAuctionId, args.nextNftTokenId)	// @audit-issue
```
*GitHub*: [165](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/RollingAuctionCrowdfund.sol#L165-L165)
```solidity
Path: ./contracts/vendor/solmate/ERC20.sol

122:                    abi.encodePacked(	// @audit-issue
123:                        "\x19\x01",
124:                        DOMAIN_SEPARATOR(),
125:                        keccak256(
126:                            abi.encode(
127:                                keccak256(
128:                                    "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
129:                                ),
130:                                owner,
131:                                spender,
132:                                value,
133:                                nonces[owner]++,
134:                                deadline
135:                            )
136:                        )
137:                    )
```
*GitHub*: [122](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC20.sol#L122-L122)
```solidity
Path: ./contracts/signature-validators/OffChainSignatureValidator.sol

48:        bytes memory encodedPacket = abi.encodePacked(	// @audit-issue
49:            "\x19Ethereum Signed Message:\n",
50:            Strings.toString(message.length),
51:            message
52:        );
```
*GitHub*: [48](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L48-L48)
</details>



### [N-14]  Contract uses both `require()`/`revert()` as well as custom errors
### Severity
* Impact: Low
* Likelihood: Low
### Description

Consider using just one method in a single file

### Number Of Instances Found
2

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

13:contract CrowdfundNFT is IERC721, IERC4906, EIP165, ReadOnlyDelegateCall {	// @audit-issue
```
*GitHub*: [13](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L13-L13)
```solidity
Path: ./contracts/crowdfund/ContributionRouter.sol

8:contract ContributionRouter {	// @audit-issue
```
*GitHub*: [8](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L8-L8)
</details>



### [N-15] Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)
### Severity
* Impact: Low
* Likelihood: Low
### Description

While the compiler knows to optimize away the exponentiation, it's still better coding practice to use idioms that do not require compiler optimization, if they exist


### Number Of Instances Found
2

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/utils/LibRenderer.sol

48:        uint256 oneUnit = 10 ** decimals;	// @audit-issue

49:        if (n < 10 ** (decimals - 2)) {	// @audit-issue
```
*GitHub*: [48](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L48-L48), [49](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L49-L49)
</details>



### [N-16] Style guide: Function ordering does not follow the Solidity style guide
### Severity
* Impact: Low
* Likelihood: Low
### Description

According to the Solidity [style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

### Number Of Instances Found
20

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/InitialETHCrowdfund.sol

204:    function batchContribute(	// @audit-issue : batchContribute should come before than contribute
```
*GitHub*: [204](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L204-L204)
```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

58:    function transferFrom(address, address, uint256) external pure alwaysRevert {}	// @audit-issue : transferFrom should come before than _initialize
```
*GitHub*: [58](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L58-L58)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

214:    function emergencyExecute(	// @audit-issue : emergencyExecute should come before than _initialize
```
*GitHub*: [214](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L214-L214)
```solidity
Path: ./contracts/crowdfund/CrowdfundFactory.sol

185:    function createInitialETHCrowdfundWithMetadata(	// @audit-issue : createInitialETHCrowdfundWithMetadata should come before than createReraiseETHCrowdfund
```
*GitHub*: [185](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L185-L185)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

162:    receive() external payable {}	// @audit-issue : receive should come before than _initialize
```
*GitHub*: [162](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L162-L162)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

175:    function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {	// @audit-issue : getCrowdfundLifecycle should come before than _initialize
```
*GitHub*: [175](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L175-L175)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfundBase.sol

192:    function getCrowdfundLifecycle() public view override returns (CrowdfundLifecycle) {	// @audit-issue : getCrowdfundLifecycle should come before than _finalize
```
*GitHub*: [192](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L192-L192)
```solidity
Path: ./contracts/crowdfund/ContributionRouter.sol

62:    function caller() external view returns (address) {	// @audit-issue : caller should come before than feePerMint
```
*GitHub*: [62](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L62-L62)
```solidity
Path: ./contracts/crowdfund/AtomicManualParty.sol

83:    function createPartyWithMetadata(	// @audit-issue : createPartyWithMetadata should come before than createParty
```
*GitHub*: [83](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L83-L83)
```solidity
Path: ./contracts/party/PartyFactory.sol

57:    function createPartyWithMetadata(	// @audit-issue : createPartyWithMetadata should come before than createParty
```
*GitHub*: [57](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyFactory.sol#L57-L57)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

76:    constructor(IGlobals globals) payable PartyGovernance(globals) ERC721("", "") {	// @audit-issue : constructor should come before than _assertAuthority
```
*GitHub*: [76](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L76-L76)
```solidity
Path: ./contracts/party/Party.sol

53:    receive() external payable {}	// @audit-issue : receive should come before than initialize
```
*GitHub*: [53](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/Party.sol#L53-L53)
```solidity
Path: ./contracts/party/PartyGovernance.sol

265:    constructor(IGlobals globals) {	// @audit-issue : constructor should come before than _assertNotGloballyDisabled
```
*GitHub*: [265](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L265-L265)
```solidity
Path: ./contracts/market-wrapper/NounsMarketWrapper.sol

59:    function getMinimumBid(uint256 auctionId) external view override returns (uint256) {	// @audit-issue : getMinimumBid should come before than auctionIdMatchesToken
```
*GitHub*: [59](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/NounsMarketWrapper.sol#L59-L59)
```solidity
Path: ./contracts/market-wrapper/KoansMarketWrapper.sol

60:    function getMinimumBid(uint256 auctionId) external view override returns (uint256) {	// @audit-issue : getMinimumBid should come before than auctionIdMatchesToken
```
*GitHub*: [60](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/KoansMarketWrapper.sol#L60-L60)
```solidity
Path: ./contracts/utils/LibRenderer.sol

78:    function generateColorHex(	// @audit-issue : generateColorHex should come before than prependNumWithZeros
```
*GitHub*: [78](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L78-L78)
```solidity
Path: ./contracts/operators/ERC20SwapOperator.sol

211:    receive() external payable {}	// @audit-issue : receive should come before than execute
```
*GitHub*: [211](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L211-L211)
```solidity
Path: ./contracts/operators/CollectionBatchBuyOperator.sol

243:    receive() external payable {}	// @audit-issue : receive should come before than _verifyTokenId
```
*GitHub*: [243](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/CollectionBatchBuyOperator.sol#L243-L243)
```solidity
Path: ./contracts/vendor/solmate/ERC721.sol

54:    constructor(string memory _name, string memory _symbol) {	// @audit-issue : constructor should come before than balanceOf
```
*GitHub*: [54](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L54-L54)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

188:    function claimFee(DistributionInfo calldata info, address payable recipient) public {	// @audit-issue : claimFee should come before than batchClaim
```
*GitHub*: [188](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L188-L188)
</details>



### [N-17] Style guide: Contract does not follow the Solidity style guide's suggested layout ordering
### Severity
* Impact: Low
* Likelihood: Low
### Description

The [style guide](https://docs.soliditylang.org/en/latest/style-guide.html#order-of-layout) says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Errors, 5) Modifiers, and 6) Functions, but the contract(s) below do not follow this ordering


### Number Of Instances Found
25

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

13:contract CrowdfundNFT is IERC721, IERC4906, EIP165, ReadOnlyDelegateCall {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> _GLOBALS
	2 -> name
	3 -> symbol
	4 -> _owners
	5 -> AlreadyMintedError
	6 -> AlreadyBurnedError
	7 -> InvalidTokenError
	8 -> InvalidAddressError
	9 -> alwaysRevert
	10 -> constructor
	11 -> _initialize
	12 -> transferFrom
	13 -> safeTransferFrom
	14 -> safeTransferFrom
	15 -> approve
	16 -> setApprovalForAll
	17 -> getApproved
	18 -> isApprovedForAll
	19 -> supportsInterface
	20 -> tokenURI
	21 -> contractURI
	22 -> ownerOf
	23 -> balanceOf
	24 -> _doesTokenExistFor
	25 -> _mint
	26 -> _burn
	27 -> _delegateToRenderer

```
*GitHub*: [13](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L13-L13)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

19:abstract contract Crowdfund is Implementation, ERC721Receiver, CrowdfundNFT {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> bytes
	2 -> uint256
	3 -> address
	4 -> DISABLE_RAGEQUIT_PERMANENTLY
	5 -> _GLOBALS
	6 -> party
	7 -> totalContributions
	8 -> gateKeeper
	9 -> gateKeeperId
	10 -> splitRecipient
	11 -> splitBps
	12 -> _splitRecipientHasBurned
	13 -> partyOptsHash
	14 -> delegationsByContributor
	15 -> _contributionsByContributor
	16 -> claims
	17 -> minContribution
	18 -> maxContribution
	19 -> emergencyExecuteDisabled
	20 -> Burned
	21 -> Contributed
	22 -> EmergencyExecute
	23 -> EmergencyExecuteDisabled
	24 -> PartyAlreadyExistsError
	25 -> WrongLifecycleError
	26 -> InvalidGovernanceOptionsError
	27 -> InvalidDelegateError
	28 -> InvalidContributorError
	29 -> NoPartyError
	30 -> NotAllowedByGateKeeperError
	31 -> SplitRecipientAlreadyBurnedError
	32 -> InvalidBpsError
	33 -> ExceedsTotalContributionsError
	34 -> NothingToClaimError
	35 -> OnlyPartyHostError
	36 -> OnlyContributorError
	37 -> MissingHostsError
	38 -> OnlyPartyDaoError
	39 -> OnlyPartyDaoOrHostError
	40 -> OnlyWhenEmergencyActionsAllowedError
	41 -> BelowMinimumContributionsError
	42 -> AboveMaximumContributionsError
	43 -> InvalidMessageValue
	44 -> constructor
	45 -> _initialize
	46 -> emergencyExecute
	47 -> disableEmergencyExecute
	48 -> burn
	49 -> activateOrRefund
	50 -> batchBurn
	51 -> batchActivateOrRefund
	52 -> claim
	53 -> contribute
	54 -> contributeFor
	55 -> batchContributeFor
	56 -> supportsInterface
	57 -> getContributorInfo
	58 -> getCrowdfundLifecycle
	59 -> _getFinalPrice
	60 -> _assertIsHost
	61 -> _isHost
	62 -> _isPartyDao
	63 -> _assertIsContributor
	64 -> _createParty
	65 -> _createParty
	66 -> _assertValidOpts
	67 -> _hashOpts
	68 -> _getFinalContribution
	69 -> _setDelegate
	70 -> _contribute
	71 -> _burn

```
*GitHub*: [19](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L19-L19)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

14:abstract contract AuctionCrowdfundBase is Crowdfund {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> IERC721
	2 -> uint256
	3 -> bytes
	4 -> nftContract
	5 -> nftTokenId
	6 -> market
	7 -> auctionId
	8 -> maximumBid
	9 -> lastBid
	10 -> expiry
	11 -> onlyHostCanBid
	12 -> _bidStatus
	13 -> Bid
	14 -> Won
	15 -> Lost
	16 -> InvalidAuctionIdError
	17 -> AuctionFinalizedError
	18 -> AlreadyHighestBidderError
	19 -> ExceedsMaximumBidError
	20 -> MinimumBidExceedsMaximumBidError
	21 -> NoContributionsError
	22 -> AuctionNotExpiredError
	23 -> constructor
	24 -> _initialize
	25 -> receive
	26 -> bid
	27 -> bid
	28 -> bid
	29 -> _bid
	30 -> getCrowdfundLifecycle
	31 -> _getFinalPrice
	32 -> _validateAuction
	33 -> _finalizeAuction

```
*GitHub*: [14](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L14-L14)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

9:contract ETHCrowdfundBase is Implementation {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> bytes
	2 -> uint256
	3 -> address
	4 -> _GLOBALS
	5 -> party
	6 -> minContribution
	7 -> maxContribution
	8 -> disableContributingForExistingCard
	9 -> fundingSplitPaid
	10 -> emergencyExecuteDisabled
	11 -> minTotalContributions
	12 -> maxTotalContributions
	13 -> totalContributions
	14 -> expiry
	15 -> exchangeRateBps
	16 -> fundingSplitBps
	17 -> fundingSplitRecipient
	18 -> gateKeeper
	19 -> gateKeeperId
	20 -> delegationsByContributor
	21 -> Contributed
	22 -> Finalized
	23 -> FundingSplitSent
	24 -> EmergencyExecuteDisabled
	25 -> EmergencyExecute
	26 -> WrongLifecycleError
	27 -> NotAllowedByGateKeeperError
	28 -> OnlyPartyHostError
	29 -> OnlyPartyDaoError
	30 -> OnlyPartyDaoOrHostError
	31 -> NotOwnerError
	32 -> OnlyWhenEmergencyActionsAllowedError
	33 -> InvalidDelegateError
	34 -> NotEnoughContributionsError
	35 -> MinGreaterThanMaxError
	36 -> MaxTotalContributionsCannotBeZeroError
	37 -> BelowMinimumContributionsError
	38 -> AboveMaximumContributionsError
	39 -> InvalidExchangeRateError
	40 -> ContributingForExistingCardDisabledError
	41 -> ZeroVotingPowerError
	42 -> FundingSplitAlreadyPaidError
	43 -> FundingSplitNotConfiguredError
	44 -> InvalidMessageValue
	45 -> constructor
	46 -> _initialize
	47 -> getCrowdfundLifecycle
	48 -> _processContribution
	49 -> convertVotingPowerToContribution
	50 -> finalize
	51 -> _finalize
	52 -> sendFundingSplit
	53 -> emergencyExecute
	54 -> disableEmergencyExecute

```
*GitHub*: [9](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L9-L9)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfundBase.sol

13:abstract contract BuyCrowdfundBase is Crowdfund {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> IERC721
	2 -> uint256
	3 -> expiry
	4 -> maximumPrice
	5 -> settledPrice
	6 -> Won
	7 -> Lost
	8 -> MaximumPriceError
	9 -> NoContributionsError
	10 -> CallProhibitedError
	11 -> FailedToBuyNFTError
	12 -> constructor
	13 -> _initialize
	14 -> _buy
	15 -> _finalize
	16 -> _finalize
	17 -> getCrowdfundLifecycle
	18 -> _getFinalPrice
	19 -> _isCallAllowed

```
*GitHub*: [13](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L13-L13)
```solidity
Path: ./contracts/crowdfund/ContributionRouter.sol

8:contract ContributionRouter {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> bytes
	2 -> address
	3 -> OWNER
	4 -> _storage
	5 -> FeePerMintUpdated
	6 -> ReceivedFees
	7 -> ClaimedFees
	8 -> OnlyOwner
	9 -> onlyOwner
	10 -> constructor
	11 -> setFeePerMint
	12 -> claimFees
	13 -> feePerMint
	14 -> caller
	15 -> fallback
	16 -> receive

```
*GitHub*: [8](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L8-L8)
```solidity
Path: ./contracts/crowdfund/RollingAuctionCrowdfund.sol

12:contract RollingAuctionCrowdfund is AuctionCrowdfundBase {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> IERC721
	2 -> uint256
	3 -> bytes
	4 -> allowedAuctionsMerkleRoot
	5 -> AuctionUpdated
	6 -> BadNextAuctionError
	7 -> constructor
	8 -> initialize
	9 -> finalize
	10 -> finalizeOrRollOver

```
*GitHub*: [12](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/RollingAuctionCrowdfund.sol#L12-L12)
```solidity
Path: ./contracts/crowdfund/AtomicManualParty.sol

12:contract AtomicManualParty {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> PARTY_FACTORY
	2 -> AtomicManualPartyCreated
	3 -> NoPartyMembers
	4 -> PartyMembersArityMismatch
	5 -> InvalidPartyMember
	6 -> InvalidPartyMemberVotingPower
	7 -> constructor
	8 -> createParty
	9 -> createPartyWithMetadata
	10 -> _issuePartyCards
	11 -> _validateAtomicManualPartyArrays

```
*GitHub*: [12](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L12-L12)
```solidity
Path: ./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol

16:contract CollectionBatchBuyCrowdfund is BuyCrowdfundBase {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> IERC721
	2 -> uint256
	3 -> bytes
	4 -> nftContract
	5 -> nftTokenIdsMerkleRoot
	6 -> NothingBoughtError
	7 -> InvalidMinTokensBoughtError
	8 -> InvalidTokenIdError
	9 -> ContributionsSpentForFailedBuyError
	10 -> NotEnoughTokensBoughtError
	11 -> NotEnoughEthUsedError
	12 -> MismatchedCallArgLengthsError
	13 -> constructor
	14 -> initialize
	15 -> batchBuy
	16 -> _verifyTokenId

```
*GitHub*: [16](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L16-L16)
```solidity
Path: ./contracts/globals/Globals.sol

8:contract Globals is IGlobals, Multicall {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> multiSig
	2 -> pendingMultiSig
	3 -> _wordValues
	4 -> _includedWordValues
	5 -> ValueSet
	6 -> IncludesSet
	7 -> PendingMultiSigSet
	8 -> MultiSigSet
	9 -> OnlyMultiSigError
	10 -> OnlyPendingMultiSigError
	11 -> InvalidBooleanValueError
	12 -> onlyMultisig
	13 -> onlyPendingMultisig
	14 -> constructor
	15 -> transferMultiSig
	16 -> acceptMultiSig
	17 -> getBytes32
	18 -> getUint256
	19 -> getBool
	20 -> getAddress
	21 -> getImplementation
	22 -> getIncludesBytes32
	23 -> getIncludesUint256
	24 -> getIncludesAddress
	25 -> setBytes32
	26 -> setUint256
	27 -> setBool
	28 -> setAddress
	29 -> setIncludesBytes32
	30 -> setIncludesUint256
	31 -> setIncludesAddress

```
*GitHub*: [8](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L8-L8)
```solidity
Path: ./contracts/party/PartyFactory.sol

16:contract PartyFactory is IPartyFactory {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> address
	2 -> _GLOBALS
	3 -> NoAuthorityError
	4 -> constructor
	5 -> createParty
	6 -> createPartyWithMetadata

```
*GitHub*: [16](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyFactory.sol#L16-L16)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

14:contract PartyGovernanceNFT is PartyGovernance, ERC721, IERC2981 {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> uint256
	2 -> uint96
	3 -> IERC20
	4 -> address
	5 -> ENABLE_RAGEQUIT_PERMANENTLY
	6 -> DISABLE_RAGEQUIT_PERMANENTLY
	7 -> ETH_ADDRESS
	8 -> _GLOBALS
	9 -> tokenCount
	10 -> mintedVotingPower
	11 -> rageQuitTimestamp
	12 -> votingPowerByTokenId
	13 -> isAuthority
	14 -> AuthorityAdded
	15 -> AuthorityRemoved
	16 -> RageQuitSet
	17 -> Burn
	18 -> RageQuit
	19 -> PartyCardIntrinsicVotingPowerSet
	20 -> FixedRageQuitTimestampError
	21 -> CannotRageQuitError
	22 -> CannotDisableRageQuitAfterInitializationError
	23 -> InvalidTokenOrderError
	24 -> BelowMinWithdrawAmountError
	25 -> NothingToBurnError
	26 -> onlySelf
	27 -> _assertAuthority
	28 -> constructor
	29 -> _initialize
	30 -> supportsInterface
	31 -> tokenURI
	32 -> contractURI
	33 -> royaltyInfo
	34 -> getDistributionShareOf
	35 -> getVotingPowerShareOf
	36 -> mint
	37 -> increaseVotingPower
	38 -> decreaseVotingPower
	39 -> increaseTotalVotingPower
	40 -> decreaseTotalVotingPower
	41 -> burn
	42 -> _burnAndUpdateVotingPower
	43 -> burn
	44 -> setRageQuit
	45 -> rageQuit
	46 -> transferFrom
	47 -> safeTransferFrom
	48 -> safeTransferFrom
	49 -> addAuthority
	50 -> abdicateAuthority
	51 -> _delegateToRenderer

```
*GitHub*: [14](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L14-L14)
```solidity
Path: ./contracts/party/PartyGovernance.sol

23:abstract contract PartyGovernance is	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> IERC20
	2 -> bytes
	3 -> uint256
	4 -> int192
	5 -> uint96
	6 -> UINT40_HIGH_BIT
	7 -> VETO_VALUE
	8 -> _GLOBALS
	9 -> emergencyExecuteDisabled
	10 -> feeBps
	11 -> feeRecipient
	12 -> lastRageQuitTimestamp
	13 -> preciousListHash
	14 -> lastProposalId
	15 -> isHost
	16 -> delegationsByVoter
	17 -> numHosts
	18 -> _proposalStateByProposalId
	19 -> _votingPowerSnapshotsByVoter
	20 -> Proposed
	21 -> ProposalAccepted
	22 -> EmergencyExecute
	23 -> ProposalPassed
	24 -> ProposalVetoed
	25 -> ProposalExecuted
	26 -> ProposalCancelled
	27 -> DistributionCreated
	28 -> PartyDelegateUpdated
	29 -> HostStatusTransferred
	30 -> EmergencyExecuteDisabled
	31 -> PartyVotingSnapshotCreated
	32 -> MismatchedPreciousListLengths
	33 -> BadProposalStatusError
	34 -> BadProposalHashError
	35 -> ExecutionTimeExceededError
	36 -> NotAuthorized
	37 -> InvalidDelegateError
	38 -> BadPreciousListError
	39 -> OnlyWhenEmergencyActionsAllowedError
	40 -> OnlyWhenEnabledError
	41 -> AlreadyVotedError
	42 -> InvalidNewHostError
	43 -> ProposalCannotBeCancelledYetError
	44 -> InvalidBpsError
	45 -> DistributionsRequireVoteError
	46 -> PartyNotStartedError
	47 -> CannotRageQuitAndAcceptError
	48 -> TooManyHosts
	49 -> onlyPartyDao
	50 -> onlyPartyDaoOrHost
	51 -> onlyWhenEmergencyExecuteAllowed
	52 -> _assertHost
	53 -> _assertActiveMember
	54 -> _assertNotGloballyDisabled
	55 -> constructor
	56 -> _initialize
	57 -> fallback
	58 -> supportsInterface
	59 -> getProposalExecutionEngine
	60 -> getProposalEngineOpts
	61 -> getVotingPowerAt
	62 -> getVotingPowerAt
	63 -> getProposalStateInfo
	64 -> getGovernanceValues
	65 -> getProposalHash
	66 -> findVotingPowerSnapshotIndex
	67 -> delegateVotingPower
	68 -> abdicateHost
	69 -> distribute
	70 -> propose
	71 -> accept
	72 -> veto
	73 -> execute
	74 -> cancel
	75 -> emergencyExecute
	76 -> disableEmergencyExecute
	77 -> _executeProposal
	78 -> _getVotingPowerSnapshotAt
	79 -> _transferVotingPower
	80 -> _adjustVotingPower
	81 -> _rebalanceDelegates
	82 -> _insertVotingPowerSnapshot
	83 -> _getLastVotingPowerSnapshotForVoter
	84 -> _getProposalFlags
	85 -> _getProposalStatus
	86 -> _isUnanimousVotes
	87 -> _hostsAccepted
	88 -> _areVotesPassing
	89 -> _setPreciousList
	90 -> _isPreciousListCorrect
	91 -> _hashPreciousList
	92 -> _validateProposalHash

```
*GitHub*: [23](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L23-L23)
```solidity
Path: ./contracts/utils/Implementation.sol

5:abstract contract Implementation {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> implementation
	2 -> initialized
	3 -> Initialized
	4 -> AlreadyInitialized
	5 -> OnlyDelegateCallError
	6 -> onlyDelegateCall
	7 -> onlyInitialize
	8 -> constructor
	9 -> IMPL

```
*GitHub*: [5](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/Implementation.sol#L5-L5)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

20:abstract contract ListOnOpenseaAdvancedProposal is OpenseaHelpers, ZoraHelpers {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> uint256
	2 -> _GLOBALS
	3 -> OpenseaOrderListed
	4 -> OpenseaAdvancedOrderListed
	5 -> OpenseaOrderSold
	6 -> OpenseaAdvancedOrderSold
	7 -> OpenseaOrderExpired
	8 -> OpenseaOrderStillActiveError
	9 -> InvalidFeeRecipients
	10 -> constructor
	11 -> _executeListOnOpenseaAdvanced
	12 -> _executeAdvancedOpenseaProposal
	13 -> _listOnOpensea
	14 -> _approveConduit
	15 -> _getOrderHash
	16 -> _cleanUpListing

```
*GitHub*: [20](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L20-L20)
```solidity
Path: ./contracts/proposals/ArbitraryCallsProposal.sol

17:contract ArbitraryCallsProposal {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> IERC721
	2 -> address
	3 -> _ZORA
	4 -> ArbitraryCallExecuted
	5 -> PreciousLostError
	6 -> CallProhibitedError
	7 -> ArbitraryCallFailedError
	8 -> UnexpectedCallResultHashError
	9 -> NotEnoughEthAttachedError
	10 -> InvalidApprovalCallLength
	11 -> constructor
	12 -> _executeArbitraryCalls
	13 -> _executeSingleArbitraryCall
	14 -> _getHasPrecious
	15 -> _isCallAllowed
	16 -> _decodeApproveCallDataArgs
	17 -> _decodeSetApprovalForAllCallDataArgs

```
*GitHub*: [17](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L17-L17)
```solidity
Path: ./contracts/proposals/FractionalizeProposal.sol

12:contract FractionalizeProposal {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> VAULT_FACTORY
	2 -> FractionalV1VaultCreated
	3 -> constructor
	4 -> _executeFractionalize

```
*GitHub*: [12](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/FractionalizeProposal.sol#L12-L12)
```solidity
Path: ./contracts/proposals/SetGovernanceParameterProposal.sol

7:contract SetGovernanceParameterProposal is ProposalStorage {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> VoteDurationSet
	2 -> ExecutionDelaySet
	3 -> PassThresholdBpsSet
	4 -> InvalidGovernanceParameter
	5 -> _executeSetGovernanceParameter

```
*GitHub*: [7](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/SetGovernanceParameterProposal.sol#L7-L7)
```solidity
Path: ./contracts/gatekeepers/TokenGateKeeper.sol

17:contract TokenGateKeeper is IGateKeeper {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> CONTRIBUTION_ROUTER
	2 -> _lastId
	3 -> gateInfo
	4 -> TokenGateCreated
	5 -> constructor
	6 -> isAllowed
	7 -> createGate

```
*GitHub*: [17](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/gatekeepers/TokenGateKeeper.sol#L17-L17)
```solidity
Path: ./contracts/operators/ERC20SwapOperator.sol

15:contract ERC20SwapOperator is IOperator {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> address
	2 -> bytes
	3 -> IERC20
	4 -> ETH_TOKEN_ADDRESS
	5 -> _GLOBALS
	6 -> isTargetAllowed
	7 -> ERC20SwapOperationExecuted
	8 -> TargetAllowedSet
	9 -> InsufficientReceivedAmountError
	10 -> OnlyPartyDaoError
	11 -> UnauthorizedTargetError
	12 -> InKindSwap
	13 -> onlyPartyDao
	14 -> constructor
	15 -> setTargetAllowed
	16 -> execute
	17 -> receive

```
*GitHub*: [15](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L15-L15)
```solidity
Path: ./contracts/vendor/solmate/ERC1155.sol

11:abstract contract ERC1155 is IERC1155 {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> balanceOf
	2 -> isApprovedForAll
	3 -> URI
	4 -> uri
	5 -> setApprovalForAll
	6 -> safeTransferFrom
	7 -> safeBatchTransferFrom
	8 -> balanceOfBatch
	9 -> supportsInterface
	10 -> _mint
	11 -> _batchMint
	12 -> _batchBurn
	13 -> _burn

```
*GitHub*: [11](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L11-L11)
```solidity
Path: ./contracts/vendor/solmate/ERC721.sol

13:abstract contract ERC721 is IERC721, EIP165 {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> name
	2 -> symbol
	3 -> _ownerOf
	4 -> _balanceOf
	5 -> getApproved
	6 -> isApprovedForAll
	7 -> tokenURI
	8 -> ownerOf
	9 -> balanceOf
	10 -> constructor
	11 -> approve
	12 -> setApprovalForAll
	13 -> transferFrom
	14 -> safeTransferFrom
	15 -> safeTransferFrom
	16 -> supportsInterface
	17 -> _mint
	18 -> _burn
	19 -> _safeMint
	20 -> _safeMint

```
*GitHub*: [13](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L13-L13)
```solidity
Path: ./contracts/authorities/AddPartyCardsAuthority.sol

7:contract AddPartyCardsAuthority {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> PartyCardAdded
	2 -> NoPartyMembers
	3 -> ArityMismatch
	4 -> InvalidPartyMember
	5 -> InvalidPartyMemberVotingPower
	6 -> addPartyCards

```
*GitHub*: [7](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/authorities/AddPartyCardsAuthority.sol#L7-L7)
```solidity
Path: ./contracts/signature-validators/OffChainSignatureValidator.sol

9:contract OffChainSignatureValidator is IERC1271 {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> signingThersholdBps
	2 -> SigningThresholdBpsSet
	3 -> NotMemberOfParty
	4 -> InsufficientVotingPower
	5 -> MessageHashMismatch
	6 -> isValidSignature
	7 -> setSigningThresholdBps

```
*GitHub*: [9](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L9-L9)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

15:contract TokenDistributor is ITokenDistributor {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> address
	2 -> IERC20
	3 -> bytes
	4 -> uint256
	5 -> NATIVE_TOKEN_ADDRESS
	6 -> GLOBALS
	7 -> EMERGENCY_DISABLED_TIMESTAMP
	8 -> lastDistributionIdPerParty
	9 -> _storedBalances
	10 -> _distributionStates
	11 -> EmergencyExecute
	12 -> OnlyPartyDaoError
	13 -> InvalidDistributionInfoError
	14 -> DistributionAlreadyClaimedByPartyTokenError
	15 -> DistributionFeeAlreadyClaimedError
	16 -> MustOwnTokenError
	17 -> TokenIdAboveMaxError
	18 -> EmergencyActionsNotAllowedError
	19 -> InvalidDistributionSupplyError
	20 -> OnlyFeeRecipientError
	21 -> InvalidFeeBpsError
	22 -> onlyPartyDao
	23 -> onlyIfEmergencyActionsAllowed
	24 -> constructor
	25 -> createNativeDistribution
	26 -> createErc20Distribution
	27 -> claim
	28 -> claimFee
	29 -> batchClaim
	30 -> batchClaimFee
	31 -> getClaimAmount
	32 -> wasFeeClaimed
	33 -> hasPartyTokenIdClaimed
	34 -> getRemainingMemberSupply
	35 -> emergencyExecute
	36 -> _createDistribution
	37 -> _transfer
	38 -> _getDistributionHash
	39 -> _getBalanceId

```
*GitHub*: [15](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L15-L15)
</details>



### [N-18] `constants` should be defined rather than using magic numbers
### Severity
* Impact: Low
* Likelihood: Low
### Description

Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

### Number Of Instances Found
72

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/InitialETHCrowdfund.sol

379:        for (uint i = 0; i < authoritiesLength - 1; ++i) {	// @audit-issue

382:        authorities[authoritiesLength - 1] = address(this);	// @audit-issue
```
*GitHub*: [379](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L379-L379), [382](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L382-L382)
```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

98:            interfaceId == 0x80ac58cd ||	// @audit-issue

100:            interfaceId == 0x49064906;	// @audit-issue
```
*GitHub*: [98](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L98-L98), [100](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L100-L100)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

181:        if (opts.governanceOpts.feeBps > 1e4) {	// @audit-issue

184:        if (opts.governanceOpts.passThresholdBps > 1e4) {	// @audit-issue

187:        if (opts.splitBps > 1e4) {	// @audit-issue

605:        votingPower = ((1e4 - splitBps_) * ethUsed) / 1e4;	// @audit-issue

609:            votingPower += (splitBps_ * totalEthUsed + (1e4 - 1)) / 1e4; // round up	// @audit-issue

686:        if (numContributions >= 1) {	// @audit-issue

694:                contributions[numContributions - 1] = lastContribution;	// @audit-issue
```
*GitHub*: [181](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L181-L181), [184](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L184-L184), [187](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L187-L187), [605](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L605-L605), [609](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L609-L609), [686](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L686-L686), [694](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L694-L694)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

266:            amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;	// @audit-issue

270:        votingPower = (amount * exchangeRateBps) / 1e4;	// @audit-issue

281:        amount = (votingPower * 1e4) / exchangeRateBps;	// @audit-issue

287:            amount = (amount * 1e4) / (1e4 - fundingSplitBps_);	// @audit-issue

325:            totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;	// @audit-issue

355:        splitAmount = (totalContributions * fundingSplitBps_) / 1e4;	// @audit-issue
```
*GitHub*: [266](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L266-L266), [270](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L270-L270), [281](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L281-L281), [287](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L287-L287), [325](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L325-L325), [355](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L355-L355)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfundBase.sol

221:        if (callTarget == address(token) && callData.length >= 4) {	// @audit-issue
```
*GitHub*: [221](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L221-L221)
```solidity
Path: ./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol

208:            tokens[tokensBought - 1] = token;	// @audit-issue

209:            args.tokenIds[tokensBought - 1] = args.tokenIds[i];	// @audit-issue
```
*GitHub*: [208](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L208-L208), [209](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L209-L209)
```solidity
Path: ./contracts/globals/Globals.sol

73:        if (value > 1) {	// @audit-issue
```
*GitHub*: [73](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L73-L73)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

157:            totalVotingPower == 0 ? 0 : (votingPowerByTokenId[tokenId] * 1e18) / totalVotingPower;	// @audit-issue

393:                    withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;	// @audit-issue
```
*GitHub*: [157](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L157-L157), [393](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L393-L393)
```solidity
Path: ./contracts/party/PartyGovernance.sol

277:        if (govOpts.feeBps > 1e4) {	// @audit-issue

280:        if (govOpts.passThresholdBps > 1e4) {	// @audit-issue

338:            interfaceId == 0x49064906;	// @audit-issue

440:                low = mid + 1;	// @audit-issue

445:        return high == 0 ? type(uint256).max : high - 1;	// @audit-issue

918:                (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)	// @audit-issue

1036:                voterSnaps[n - 1] = snap;	// @audit-issue

1049:            snap = voterSnaps[n - 1];	// @audit-issue

1119:        return acceptanceRatio >= 0.9999e4;	// @audit-issue

1134:        return (uint256(voteCount) * 1e4) / uint256(totalVotingPower) >= uint256(passThresholdBps);	// @audit-issue
```
*GitHub*: [277](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L277-L277), [280](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L280-L280), [338](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L338-L338), [440](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L440-L440), [445](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L445-L445), [918](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L918-L918), [1036](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1036-L1036), [1049](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1049-L1049), [1119](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1119-L1119), [1134](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1134-L1134)
```solidity
Path: ./contracts/market-wrapper/NounsMarketWrapper.sol

69:        return amount + ((amount * minBidIncrementPercentage) / 100);	// @audit-issue
```
*GitHub*: [69](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/NounsMarketWrapper.sol#L69-L69)
```solidity
Path: ./contracts/market-wrapper/KoansMarketWrapper.sol

70:        return amount + ((amount * minBidIncrementPercentage) / 100);	// @audit-issue
```
*GitHub*: [70](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/KoansMarketWrapper.sol#L70-L70)
```solidity
Path: ./contracts/utils/LibRenderer.sol

31:        translateX = bytes(partyName).length * 30 + 300;	// @audit-issue

32:        duration = translateX / 56;	// @audit-issue

36:        if (duration % 2 != 0) duration += 1;	// @audit-issue

38:        delay = duration / 2;	// @audit-issue

39:        steps = translateX / 6;	// @audit-issue

49:        if (n < 10 ** (decimals - 2)) {	// @audit-issue

55:                string.concat("0.", prependNumWithZeros(str, decimals).substring(0, maxChars - 1));	// @audit-issue

56:        } else if (n >= 1000 * oneUnit) {	// @audit-issue
```
*GitHub*: [31](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L31-L31), [32](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L32-L32), [36](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L36-L36), [38](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L38-L38), [39](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L39-L39), [49](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L49-L49), [55](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L55-L55), [56](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L56-L56)
```solidity
Path: ./contracts/utils/LibSafeERC721.sol

21:        if (!s || r.length < 32) {	// @audit-issue
```
*GitHub*: [21](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibSafeERC721.sol#L21-L21)
```solidity
Path: ./contracts/utils/PartyHelpers.sol

82:                count = tokenCount + 1 - startTokenId;	// @audit-issue
```
*GitHub*: [82](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/PartyHelpers.sol#L82-L82)
```solidity
Path: ./contracts/utils/vendor/Strings.sol

23:            temp /= 10;	// @audit-issue

27:            digits -= 1;	// @audit-issue

28:            buffer[digits] = bytes1(uint8(48 + uint256(value % 10)));	// @audit-issue

29:            value /= 10;	// @audit-issue

45:            temp >>= 8;	// @audit-issue

58:            buffer[i] = _HEX_SYMBOLS[value & 0xf];	// @audit-issue

59:            value >>= 4;	// @audit-issue

57:        for (uint256 i = 2 * length + 1; i > 1; --i) {	// @audit-issue
```
*GitHub*: [23](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L23-L23), [27](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L27-L27), [28](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L28-L28), [29](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L29-L29), [45](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L45-L45), [58](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L58-L58), [59](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L59-L59), [57](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L57-L57)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

289:        orderParams.totalOriginalConsiderationItems = 1 + data.fees.length;	// @audit-issue

301:        orderParams.consideration = new IOpenseaExchange.ConsiderationItem[](1 + data.fees.length);	// @audit-issue

310:            cons = orderParams.consideration[1 + i];	// @audit-issue
```
*GitHub*: [289](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L289-L289), [301](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L301-L301), [310](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L310-L310)
```solidity
Path: ./contracts/proposals/ArbitraryCallsProposal.sol

171:        if (call.data.length >= 4) {	// @audit-issue

211:                        return callIndex + 1 == callsCount;	// @audit-issue

236:        if (callData.length < 68) {	// @audit-issue

249:        if (callData.length < 68) {	// @audit-issue
```
*GitHub*: [171](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L171-L171), [211](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L211-L211), [236](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L236-L236), [249](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ArbitraryCallsProposal.sol#L249-L249)
```solidity
Path: ./contracts/proposals/SetGovernanceParameterProposal.sol

33:            if (proposalData.voteDuration < 1 hours) {	// @audit-issue

43:            if (proposalData.executionDelay > 30 days) {	// @audit-issue

54:            if (proposalData.passThresholdBps > 10000) {	// @audit-issue
```
*GitHub*: [33](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/SetGovernanceParameterProposal.sol#L33-L33), [43](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/SetGovernanceParameterProposal.sol#L43-L43), [54](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/SetGovernanceParameterProposal.sol#L54-L54)
```solidity
Path: ./contracts/vendor/solmate/ERC1155.sol

138:            interfaceId == 0x01ffc9a7 || // ERC165 Interface ID for ERC165	// @audit-issue

139:            interfaceId == 0xd9b67a26 || // ERC165 Interface ID for ERC1155	// @audit-issue

140:            interfaceId == 0x0e89341c; // ERC165 Interface ID for ERC1155MetadataURI	// @audit-issue
```
*GitHub*: [138](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L138-L138), [139](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L139-L139), [140](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L140-L140)
```solidity
Path: ./contracts/vendor/solmate/ERC721.sol

141:            interfaceId == 0x80ac58cd || // ERC165 Interface ID for ERC721	// @audit-issue

142:            interfaceId == 0x5b5e139f; // ERC165 Interface ID for ERC721Metadata	// @audit-issue
```
*GitHub*: [141](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L141-L141), [142](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L142-L142)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

259:        if (success && abi.decode(response, (uint16)) >= 1) {	// @audit-issue

265:                ((shareOfSupply * info.memberSupply + (1e18 - 1)) / 1e18)	// @audit-issue

276:                ((shareOfSupply * info.memberSupply + (1e18 - 1)) / 1e18)	// @audit-issue

321:        if (args.feeBps > 1e4) {	// @audit-issue
```
*GitHub*: [259](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L259-L259), [265](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L265-L265), [276](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L276-L276), [321](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L321-L321)
</details>



### [N-19] Custom error has no error details
### Severity
* Impact: Low
* Likelihood: Low
### Description

Consider adding parameters to the error to indicate which user or values caused the failure

### Number Of Instances Found
67

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

17:    error InvalidAddressError();	// @audit-issue
```
*GitHub*: [17](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L17-L17)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

91:    error InvalidGovernanceOptionsError();	// @audit-issue

92:    error InvalidDelegateError();	// @audit-issue

93:    error InvalidContributorError();	// @audit-issue

94:    error NoPartyError();	// @audit-issue

101:    error SplitRecipientAlreadyBurnedError();	// @audit-issue

104:    error NothingToClaimError();	// @audit-issue

105:    error OnlyPartyHostError();	// @audit-issue

106:    error OnlyContributorError();	// @audit-issue

107:    error MissingHostsError();	// @audit-issue

110:    error OnlyWhenEmergencyActionsAllowedError();	// @audit-issue

113:    error InvalidMessageValue();	// @audit-issue
```
*GitHub*: [91](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L91-L91), [92](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L92-L92), [93](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L93-L93), [94](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L94-L94), [101](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L101-L101), [104](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L104-L104), [105](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L105-L105), [106](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L106-L106), [107](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L107-L107), [110](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L110-L110), [113](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L113-L113)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

86:    error InvalidAuctionIdError();	// @audit-issue

88:    error AlreadyHighestBidderError();	// @audit-issue

91:    error NoContributionsError();	// @audit-issue

92:    error AuctionNotExpiredError();	// @audit-issue
```
*GitHub*: [86](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L86-L86), [88](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L88-L88), [91](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L91-L91), [92](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L92-L92)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

57:    error OnlyPartyHostError();	// @audit-issue

61:    error OnlyWhenEmergencyActionsAllowedError();	// @audit-issue

62:    error InvalidDelegateError();	// @audit-issue

69:    error ContributingForExistingCardDisabledError();	// @audit-issue

70:    error ZeroVotingPowerError();	// @audit-issue

71:    error FundingSplitAlreadyPaidError();	// @audit-issue

72:    error FundingSplitNotConfiguredError();	// @audit-issue

73:    error InvalidMessageValue();	// @audit-issue
```
*GitHub*: [57](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L57-L57), [61](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L61-L61), [62](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L62-L62), [69](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L69-L69), [70](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L70-L70), [71](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L71-L71), [72](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L72-L72), [73](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L73-L73)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfundBase.sol

62:    error NoContributionsError();	// @audit-issue
```
*GitHub*: [62](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L62-L62)
```solidity
Path: ./contracts/crowdfund/ContributionRouter.sol

16:    error OnlyOwner();	// @audit-issue
```
*GitHub*: [16](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L16-L16)
```solidity
Path: ./contracts/crowdfund/RollingAuctionCrowdfund.sol

39:    error BadNextAuctionError();	// @audit-issue
```
*GitHub*: [39](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/RollingAuctionCrowdfund.sol#L39-L39)
```solidity
Path: ./contracts/crowdfund/AtomicManualParty.sol

25:    error NoPartyMembers();	// @audit-issue

27:    error PartyMembersArityMismatch();	// @audit-issue

29:    error InvalidPartyMember();	// @audit-issue

31:    error InvalidPartyMemberVotingPower();	// @audit-issue
```
*GitHub*: [25](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L25-L25), [27](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L27-L27), [29](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L29-L29), [31](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L31-L31)
```solidity
Path: ./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol

80:    error NothingBoughtError();	// @audit-issue

82:    error InvalidTokenIdError();	// @audit-issue

83:    error ContributionsSpentForFailedBuyError();	// @audit-issue

86:    error MismatchedCallArgLengthsError();	// @audit-issue
```
*GitHub*: [80](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L80-L80), [82](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L82-L82), [83](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L83-L83), [86](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L86-L86)
```solidity
Path: ./contracts/globals/Globals.sol

16:    error OnlyMultiSigError();	// @audit-issue

17:    error OnlyPendingMultiSigError();	// @audit-issue
```
*GitHub*: [16](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L16-L16), [17](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L17-L17)
```solidity
Path: ./contracts/party/PartyFactory.sol

19:    error NoAuthorityError();	// @audit-issue
```
*GitHub*: [19](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyFactory.sol#L19-L19)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

22:    error CannotDisableRageQuitAfterInitializationError();	// @audit-issue

23:    error InvalidTokenOrderError();	// @audit-issue

25:    error NothingToBurnError();	// @audit-issue
```
*GitHub*: [22](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L22-L22), [23](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L23-L23), [25](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L25-L25)
```solidity
Path: ./contracts/party/PartyGovernance.sol

168:    error MismatchedPreciousListLengths();	// @audit-issue

172:    error NotAuthorized();	// @audit-issue

173:    error InvalidDelegateError();	// @audit-issue

174:    error BadPreciousListError();	// @audit-issue

175:    error OnlyWhenEmergencyActionsAllowedError();	// @audit-issue

176:    error OnlyWhenEnabledError();	// @audit-issue

178:    error InvalidNewHostError();	// @audit-issue

181:    error DistributionsRequireVoteError();	// @audit-issue

182:    error PartyNotStartedError();	// @audit-issue

183:    error CannotRageQuitAndAcceptError();	// @audit-issue

184:    error TooManyHosts();	// @audit-issue
```
*GitHub*: [168](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L168-L168), [172](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L172-L172), [173](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L173-L173), [174](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L174-L174), [175](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L175-L175), [176](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L176-L176), [178](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L178-L178), [181](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L181-L181), [182](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L182-L182), [183](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L183-L183), [184](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L184-L184)
```solidity
Path: ./contracts/utils/Implementation.sol

8:    error AlreadyInitialized();	// @audit-issue

9:    error OnlyDelegateCallError();	// @audit-issue
```
*GitHub*: [8](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/Implementation.sol#L8-L8), [9](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/Implementation.sol#L9-L9)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

82:    error InvalidFeeRecipients();	// @audit-issue
```
*GitHub*: [82](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L82-L82)
```solidity
Path: ./contracts/operators/ERC20SwapOperator.sol

59:    error InKindSwap();	// @audit-issue
```
*GitHub*: [59](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L59-L59)
```solidity
Path: ./contracts/operators/CollectionBatchBuyOperator.sol

73:    error NothingBoughtError();	// @audit-issue

75:    error InvalidTokenIdError();	// @audit-issue

83:    error TokenIdsNotSorted();	// @audit-issue
```
*GitHub*: [73](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/CollectionBatchBuyOperator.sol#L73-L73), [75](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/CollectionBatchBuyOperator.sol#L75-L75), [83](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/CollectionBatchBuyOperator.sol#L83-L83)
```solidity
Path: ./contracts/authorities/AddPartyCardsAuthority.sol

9:    error NoPartyMembers();	// @audit-issue

11:    error ArityMismatch();	// @audit-issue

13:    error InvalidPartyMember();	// @audit-issue

15:    error InvalidPartyMemberVotingPower();	// @audit-issue
```
*GitHub*: [9](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/authorities/AddPartyCardsAuthority.sol#L9-L9), [11](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/authorities/AddPartyCardsAuthority.sol#L11-L11), [13](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/authorities/AddPartyCardsAuthority.sol#L13-L13), [15](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/authorities/AddPartyCardsAuthority.sol#L15-L15)
```solidity
Path: ./contracts/signature-validators/OffChainSignatureValidator.sol

10:    error NotMemberOfParty();	// @audit-issue

11:    error InsufficientVotingPower();	// @audit-issue

12:    error MessageHashMismatch();	// @audit-issue
```
*GitHub*: [10](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L10-L10), [11](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L11-L11), [12](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L12-L12)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

54:    error EmergencyActionsNotAllowedError();	// @audit-issue
```
*GitHub*: [54](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L54-L54)
</details>



### [N-20] Constant redefined elsewhere
### Severity
* Impact: Low
* Likelihood: Low
### Description
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

### Number Of Instances Found
2

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

35:    uint40 private constant DISABLE_RAGEQUIT_PERMANENTLY = 0xab2cb21860; // uint40(uint256(keccak256("DISABLE_RAGEQUIT_PERMANENTLY")))	// @audit-issue seen in: ./contracts/crowdfund/Crowdfund.sol
```
*GitHub*: [35](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L35-L35)
```solidity
Path: ./contracts/utils/PartyHelpers.sol

32:    address private constant ETH_ADDRESS = 0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE;	// @audit-issue seen in: ./contracts/party/PartyGovernanceNFT.sol
```
*GitHub*: [32](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/PartyHelpers.sol#L32-L32)
</details>



### [N-21] Constants in comparisons should appear on the left side
### Severity
* Impact: Low
* Likelihood: Low
### Description

Doing so will prevent [typo bugs](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html)

### Number Of Instances Found
69

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/InitialETHCrowdfund.sol

298:        if (amount == 0) return 0;	// @audit-issue

300:        if (tokenId == 0) {	// @audit-issue
```
*GitHub*: [298](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L298-L298), [300](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L300-L300)
```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

98:            interfaceId == 0x80ac58cd ||	// @audit-issue

100:            interfaceId == 0x49064906;	// @audit-issue
```
*GitHub*: [98](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L98-L98), [100](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L100-L100)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

306:        if (claimInfo.refund == 0 && claimInfo.governanceTokenId == 0) {	// @audit-issue

473:        if (_contributionsByContributor[who].length == 0) {	// @audit-issue

640:        if (amount == 0) return;	// @audit-issue

703:        if (numContributions == 0) {	// @audit-issue
```
*GitHub*: [306](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L306-L306), [473](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L473-L473), [640](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L640-L640), [703](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L703-L703)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

123:        if (opts.onlyHostCanBid && opts.governanceOpts.hosts.length == 0) {	// @audit-issue
```
*GitHub*: [123](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L123-L123)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfund.sol

82:        if (opts.onlyHostCanBuy && opts.governanceOpts.hosts.length == 0) {	// @audit-issue
```
*GitHub*: [82](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfund.sol#L82-L82)
```solidity
Path: ./contracts/crowdfund/CollectionBuyCrowdfund.sol

75:        if (opts.governanceOpts.hosts.length == 0) {	// @audit-issue
```
*GitHub*: [75](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBuyCrowdfund.sol#L75-L75)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

153:        if (opts.maxTotalContributions == 0) {	// @audit-issue

165:        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);	// @audit-issue

176:        if (maxTotalContributions == 0) {	// @audit-issue

181:        if (expiry_ == 0) {	// @audit-issue

213:        if (amount == 0) return 0;	// @audit-issue

272:        if (votingPower == 0) revert ZeroVotingPowerError();	// @audit-issue

348:        if (fundingSplitRecipient_ == address(0) || fundingSplitBps_ == 0) {	// @audit-issue
```
*GitHub*: [153](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L153-L153), [165](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L165-L165), [176](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L176-L176), [181](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L181-L181), [213](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L213-L213), [272](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L272-L272), [348](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L348-L348)
```solidity
Path: ./contracts/crowdfund/AtomicManualParty.sol

151:        if (partyMembers.length == 0) {	// @audit-issue

159:            if (partyMemberVotingPowers[i] == 0) {	// @audit-issue
```
*GitHub*: [151](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L151-L151), [159](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L159-L159)
```solidity
Path: ./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol

104:        if (opts.governanceOpts.hosts.length == 0) {	// @audit-issue

146:        if (args.minTokensBought == 0) {	// @audit-issue

215:        if (totalEthUsed == 0) revert NothingBoughtError();	// @audit-issue
```
*GitHub*: [104](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L104-L104), [146](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L146-L146), [215](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L215-L215)
```solidity
Path: ./contracts/party/PartyFactory.sol

40:        if (authorities.length == 0) {	// @audit-issue
```
*GitHub*: [40](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyFactory.sol#L40-L40)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

157:            totalVotingPower == 0 ? 0 : (votingPowerByTokenId[tokenId] * 1e18) / totalVotingPower;	// @audit-issue

350:        if (tokenIds.length == 0) revert NothingToBurnError();	// @audit-issue
```
*GitHub*: [157](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L157-L157), [350](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L350-L350)
```solidity
Path: ./contracts/party/PartyGovernance.sol

225:        if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {	// @audit-issue

338:            interfaceId == 0x49064906;	// @audit-issue

445:        return high == 0 ? type(uint256).max : high - 1;	// @audit-issue

505:            if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {	// @audit-issue

510:        if (_getSharedProposalStorage().governanceValues.totalVotingPower == 0) {	// @audit-issue

644:            values.passedTime == 0 &&	// @audit-issue

900:        return nextProgressData.length == 0;	// @audit-issue

1068:        if (pv.proposedTime == 0) {	// @audit-issue

1073:            if (pv.completedTime == 0) {	// @audit-issue
```
*GitHub*: [225](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L225-L225), [338](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L338-L338), [445](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L445-L445), [505](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L505-L505), [510](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L510-L510), [644](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L644-L644), [900](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L900-L900), [1068](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1068-L1068), [1073](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L1073-L1073)
```solidity
Path: ./contracts/utils/LibERC20Compat.sol

18:            if (r.length == 0) {	// @audit-issue

23:                if (cs == 0) {	// @audit-issue

41:            if (r.length == 0) {	// @audit-issue

46:                if (cs == 0) {	// @audit-issue

63:            if (r.length == 0) {	// @audit-issue

68:                if (cs == 0) {	// @audit-issue
```
*GitHub*: [18](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibERC20Compat.sol#L18-L18), [23](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibERC20Compat.sol#L23-L23), [41](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibERC20Compat.sol#L41-L41), [46](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibERC20Compat.sol#L46-L46), [63](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibERC20Compat.sol#L63-L63), [68](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibERC20Compat.sol#L68-L68)
```solidity
Path: ./contracts/utils/LibAddress.sol

9:        if (amount == 0) return;	// @audit-issue
```
*GitHub*: [9](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibAddress.sol#L9-L9)
```solidity
Path: ./contracts/utils/vendor/Base64.sol

15:        if (len == 0) return "";	// @audit-issue
```
*GitHub*: [15](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Base64.sol#L15-L15)
```solidity
Path: ./contracts/utils/vendor/Strings.sol

16:        if (value == 0) {	// @audit-issue

38:        if (value == 0) {	// @audit-issue

61:        require(value == 0, "Strings: hex length insufficient");	// @audit-issue
```
*GitHub*: [16](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L16-L16), [38](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L38-L38), [61](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L61-L61)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

143:        ListOnOpenseaStep step = params.progressData.length == 0	// @audit-issue
```
*GitHub*: [143](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L143-L143)
```solidity
Path: ./contracts/proposals/OperatorProposal.sol

99:        if (allowedExecutors.length == 0) return;	// @audit-issue
```
*GitHub*: [99](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/OperatorProposal.sol#L99-L99)
```solidity
Path: ./contracts/proposals/AddAuthorityProposal.sol

27:        if (data.callData.length == 0) {	// @audit-issue
```
*GitHub*: [27](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/AddAuthorityProposal.sol#L27-L27)
```solidity
Path: ./contracts/operators/CollectionBatchBuyOperator.sol

104:        if (op.minTokensBought == 0) {	// @audit-issue

190:        if (totalEthUsed == 0) revert NothingBoughtError();	// @audit-issue
```
*GitHub*: [104](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/CollectionBatchBuyOperator.sol#L104-L104), [190](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/CollectionBatchBuyOperator.sol#L190-L190)
```solidity
Path: ./contracts/vendor/solmate/ERC1155.sol

57:            to.code.length == 0	// @audit-issue

102:            to.code.length == 0	// @audit-issue

138:            interfaceId == 0x01ffc9a7 || // ERC165 Interface ID for ERC165	// @audit-issue

139:            interfaceId == 0xd9b67a26 || // ERC165 Interface ID for ERC1155	// @audit-issue

140:            interfaceId == 0x0e89341c; // ERC165 Interface ID for ERC1155MetadataURI	// @audit-issue

153:            to.code.length == 0	// @audit-issue

189:            to.code.length == 0	// @audit-issue
```
*GitHub*: [57](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L57-L57), [102](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L102-L102), [138](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L138-L138), [139](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L139-L139), [140](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L140-L140), [153](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L153-L153), [189](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L189-L189)
```solidity
Path: ./contracts/vendor/solmate/ERC721.sol

110:            to.code.length == 0 ||	// @audit-issue

126:            to.code.length == 0 ||	// @audit-issue

141:            interfaceId == 0x80ac58cd || // ERC165 Interface ID for ERC721	// @audit-issue

142:            interfaceId == 0x5b5e139f; // ERC165 Interface ID for ERC721Metadata	// @audit-issue

189:            to.code.length == 0 ||	// @audit-issue

200:            to.code.length == 0 ||	// @audit-issue
```
*GitHub*: [110](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L110-L110), [126](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L126-L126), [141](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L141-L141), [142](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L142-L142), [189](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L189-L189), [200](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L200-L200)
```solidity
Path: ./contracts/authorities/AddPartyCardsAuthority.sol

35:        if (newPartyMembersLength == 0) {	// @audit-issue

47:            if (newPartyMemberVotingPowers[i] == 0) {	// @audit-issue
```
*GitHub*: [35](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/authorities/AddPartyCardsAuthority.sol#L35-L35), [47](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/authorities/AddPartyCardsAuthority.sol#L47-L47)
```solidity
Path: ./contracts/signature-validators/OffChainSignatureValidator.sol

62:        if (signerVotingPowerBps == 0 && party.balanceOf(signer) == 0) {	// @audit-issue

72:            thresholdBps == 0 ||	// @audit-issue
```
*GitHub*: [62](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L62-L62), [72](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L72-L72)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

330:            if (supply == 0) {	// @audit-issue
```
*GitHub*: [330](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L330-L330)
</details>



### [N-22] Events should use parameters to convey information
### Severity
* Impact: Low
* Likelihood: Low
### Description

For example, rather than using `event Paused()` and `event Unpaused()`, use `event PauseState(address indexed whoChangedIt, bool wasPaused, bool isNowPaused)`


### Number Of Instances Found
7

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

124:    event EmergencyExecuteDisabled();	// @audit-issue
```
*GitHub*: [124](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L124-L124)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

84:    event Lost();	// @audit-issue
```
*GitHub*: [84](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L84-L84)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

81:    event Finalized();	// @audit-issue

83:    event EmergencyExecuteDisabled();	// @audit-issue
```
*GitHub*: [81](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L81-L81), [83](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L83-L83)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfundBase.sol

59:    event Lost();	// @audit-issue
```
*GitHub*: [59](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L59-L59)
```solidity
Path: ./contracts/party/PartyGovernance.sol

159:    event EmergencyExecuteDisabled();	// @audit-issue
```
*GitHub*: [159](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L159-L159)
```solidity
Path: ./contracts/utils/Implementation.sol

6:    event Initialized();	// @audit-issue
```
*GitHub*: [6](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/Implementation.sol#L6-L6)
</details>



### [N-23] Contract should expose an `interface`
### Severity
* Impact: Low
* Likelihood: Low
### Description

All `external`/`public` functions should extend an `interface`. This is useful to make sure that the whole API is extracted.


### Number Of Instances Found
138

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/InitialETHCrowdfund.sol

108:    function initialize(
109:        InitialETHCrowdfundOptions memory crowdfundOpts,
110:        ETHPartyOptions memory partyOpts,
111:        MetadataProvider customMetadataProvider,
112:        bytes memory customMetadata
113:    ) external payable onlyInitialize {	// @audit-issue

164:    function contribute(
165:        address delegate,
166:        bytes memory gateData
167:    ) public payable onlyDelegateCall returns (uint96 votingPower) {	// @audit-issue

185:    function contribute(
186:        uint256 tokenId,
187:        address delegate,
188:        bytes memory gateData
189:    ) public payable onlyDelegateCall returns (uint96 votingPower) {	// @audit-issue

204:    function batchContribute(
205:        BatchContributeArgs calldata args
206:    ) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {	// @audit-issue

235:    function contributeFor(
236:        uint256 tokenId,
237:        address payable recipient,
238:        address initialDelegate,
239:        bytes memory gateData
240:    ) external payable onlyDelegateCall returns (uint96 votingPower) {	// @audit-issue

255:    function batchContributeFor(
256:        BatchContributeForArgs calldata args
257:    ) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {	// @audit-issue

318:    function refund(uint256 tokenId) external returns (uint96 amount) {	// @audit-issue

350:    function batchRefund(
351:        uint256[] calldata tokenIds,
352:        bool revertOnFailure
353:    ) external returns (uint96[] memory amounts) {	// @audit-issue
```
*GitHub*: [113](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L113-L113), [167](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L167-L167), [189](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L189-L189), [206](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L206-L206), [240](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L240-L240), [257](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L257-L257), [318](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L318-L318), [353](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L353-L353)
```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

104:    function tokenURI(uint256) external view returns (string memory) {	// @audit-issue

109:    function contractURI() external view returns (string memory) {	// @audit-issue
```
*GitHub*: [104](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L104-L104), [109](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L109-L109)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

214:    function emergencyExecute(
215:        address targetAddress,
216:        bytes calldata targetCallData,
217:        uint256 amountEth
218:    ) external payable onlyDelegateCall {	// @audit-issue

240:    function disableEmergencyExecute(
241:        FixedGovernanceOpts memory governanceOpts,
242:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
243:        uint256 hostIndex
244:    ) external onlyDelegateCall {	// @audit-issue

264:    function burn(address payable contributor) public {	// @audit-issue

269:    function activateOrRefund(address payable contributor) external {	// @audit-issue

277:    function batchBurn(address payable[] calldata contributors, bool revertOnFailure) public {	// @audit-issue

289:    function batchActivateOrRefund(
290:        address payable[] calldata contributors,
291:        bool revertOnFailure
292:    ) external {	// @audit-issue

302:    function claim(address payable receiver) external {	// @audit-issue

325:    function contribute(address delegate, bytes memory gateData) external payable onlyDelegateCall {	// @audit-issue

348:    function contributeFor(
349:        address recipient,
350:        address initialDelegate,
351:        bytes memory gateData
352:    ) external payable onlyDelegateCall {	// @audit-issue

370:    function batchContributeFor(
371:        address[] memory recipients,
372:        address[] memory initialDelegates,
373:        uint96[] memory values,
374:        bytes[] memory gateDatas
375:    ) external payable {	// @audit-issue

410:    function getContributorInfo(
411:        address contributor
412:    )	// @audit-issue

431:    function getCrowdfundLifecycle() public view virtual returns (CrowdfundLifecycle lifecycle);	// @audit-issue
```
*GitHub*: [218](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L218-L218), [244](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L244-L244), [264](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L264-L264), [269](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L269-L269), [277](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L277-L277), [292](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L292-L292), [302](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L302-L302), [325](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L325-L325), [352](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L352-L352), [375](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L375-L375), [412](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L412-L412), [431](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L431-L431)
```solidity
Path: ./contracts/crowdfund/CrowdfundFactory.sol

70:    function createBuyCrowdfund(
71:        BuyCrowdfund crowdfundImpl,
72:        BuyCrowdfund.BuyCrowdfundOptions memory opts,
73:        bytes memory createGateCallData
74:    ) external payable returns (BuyCrowdfund inst) {	// @audit-issue

88:    function createAuctionCrowdfund(
89:        AuctionCrowdfund crowdfundImpl,
90:        AuctionCrowdfundBase.AuctionCrowdfundOptions memory opts,
91:        bytes memory createGateCallData
92:    ) external payable returns (AuctionCrowdfund inst) {	// @audit-issue

106:    function createRollingAuctionCrowdfund(
107:        RollingAuctionCrowdfund crowdfundImpl,
108:        AuctionCrowdfundBase.AuctionCrowdfundOptions memory opts,
109:        bytes32 allowedAuctionsMerkleRoot,
110:        bytes memory createGateCallData
111:    ) external payable returns (RollingAuctionCrowdfund inst) {	// @audit-issue

124:    function createCollectionBuyCrowdfund(
125:        CollectionBuyCrowdfund crowdfundImpl,
126:        CollectionBuyCrowdfund.CollectionBuyCrowdfundOptions memory opts,
127:        bytes memory createGateCallData
128:    ) external payable returns (CollectionBuyCrowdfund inst) {	// @audit-issue

141:    function createCollectionBatchBuyCrowdfund(
142:        CollectionBatchBuyCrowdfund crowdfundImpl,
143:        CollectionBatchBuyCrowdfund.CollectionBatchBuyCrowdfundOptions memory opts,
144:        bytes memory createGateCallData
145:    ) external payable returns (CollectionBatchBuyCrowdfund inst) {	// @audit-issue

160:    function createInitialETHCrowdfund(
161:        InitialETHCrowdfund crowdfundImpl,
162:        InitialETHCrowdfund.InitialETHCrowdfundOptions memory crowdfundOpts,
163:        InitialETHCrowdfund.ETHPartyOptions memory partyOpts,
164:        bytes memory createGateCallData
165:    ) external payable returns (InitialETHCrowdfund inst) {	// @audit-issue

185:    function createInitialETHCrowdfundWithMetadata(
186:        InitialETHCrowdfund crowdfundImpl,
187:        InitialETHCrowdfund.InitialETHCrowdfundOptions memory crowdfundOpts,
188:        InitialETHCrowdfund.ETHPartyOptions memory partyOpts,
189:        MetadataProvider customMetadataProvider,
190:        bytes memory customMetadata,
191:        bytes memory createGateCallData
192:    ) public payable returns (InitialETHCrowdfund inst) {	// @audit-issue

214:    function createReraiseETHCrowdfund(
215:        ReraiseETHCrowdfund crowdfundImpl,
216:        ETHCrowdfundBase.ETHCrowdfundOptions memory opts,
217:        bytes memory createGateCallData
218:    ) external payable returns (ReraiseETHCrowdfund inst) {	// @audit-issue
```
*GitHub*: [74](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L74-L74), [92](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L92-L92), [111](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L111-L111), [128](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L128-L128), [145](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L145-L145), [165](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L165-L165), [192](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L192-L192), [218](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundFactory.sol#L218-L218)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

162:    receive() external payable {}	// @audit-issue

168:    function bid() external {	// @audit-issue

187:    function bid(
188:        FixedGovernanceOpts memory governanceOpts,
189:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
190:        uint256 hostIndex
191:    ) external {	// @audit-issue

216:    function bid(
217:        uint96 amount,
218:        FixedGovernanceOpts memory governanceOpts,
219:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
220:        uint256 hostIndex
221:    ) external {	// @audit-issue
```
*GitHub*: [162](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L162-L162), [168](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L168-L168), [191](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L191-L191), [221](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L221-L221)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfund.sol

81:    function initialize(BuyCrowdfundOptions memory opts) external payable onlyInitialize {	// @audit-issue

122:    function buy(
123:        address payable callTarget,
124:        uint96 callValue,
125:        bytes memory callData,
126:        FixedGovernanceOpts memory governanceOpts,
127:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
128:        uint256 hostIndex
129:    ) external onlyDelegateCall returns (Party party_) {	// @audit-issue
```
*GitHub*: [81](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfund.sol#L81-L81), [129](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfund.sol#L129-L129)
```solidity
Path: ./contracts/crowdfund/CollectionBuyCrowdfund.sol

74:    function initialize(CollectionBuyCrowdfundOptions memory opts) external payable onlyInitialize {	// @audit-issue

113:    function buy(
114:        uint256 tokenId,
115:        address payable callTarget,
116:        uint96 callValue,
117:        bytes memory callData,
118:        FixedGovernanceOpts memory governanceOpts,
119:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
120:        uint256 hostIndex
121:    ) external onlyDelegateCall returns (Party party_) {	// @audit-issue
```
*GitHub*: [74](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBuyCrowdfund.sol#L74-L74), [121](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBuyCrowdfund.sol#L121-L121)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfund.sol

20:    function initialize(AuctionCrowdfundOptions memory opts) external payable onlyInitialize {	// @audit-issue

33:    function finalize(
34:        FixedGovernanceOpts memory governanceOpts,
35:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts
36:    ) external onlyDelegateCall returns (Party party_) {	// @audit-issue
```
*GitHub*: [20](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfund.sol#L20-L20), [36](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfund.sol#L36-L36)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

175:    function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {	// @audit-issue

278:    function convertVotingPowerToContribution(
279:        uint96 votingPower
280:    ) public view returns (uint96 amount) {	// @audit-issue

291:    function finalize() external {	// @audit-issue

339:    function sendFundingSplit() external returns (uint96 splitAmount) {	// @audit-issue

366:    function emergencyExecute(
367:        address targetAddress,
368:        bytes calldata targetCallData,
369:        uint256 amountEth
370:    ) external payable {	// @audit-issue

388:    function disableEmergencyExecute() external {	// @audit-issue
```
*GitHub*: [175](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L175-L175), [280](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L280-L280), [291](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L291-L291), [339](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L339-L339), [370](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L370-L370), [388](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L388-L388)
```solidity
Path: ./contracts/crowdfund/ContributionRouter.sol

40:    function setFeePerMint(uint96 newFeePerMint) external onlyOwner {	// @audit-issue

48:    function claimFees(address payable recipient) external onlyOwner {	// @audit-issue

57:    function feePerMint() external view returns (uint96) {	// @audit-issue

62:    function caller() external view returns (address) {	// @audit-issue

71:    fallback() external payable {	// @audit-issue

94:    receive() external payable {	// @audit-issue
```
*GitHub*: [40](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L40-L40), [48](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L48-L48), [57](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L57-L57), [62](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L62-L62), [71](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L71-L71), [94](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ContributionRouter.sol#L94-L94)
```solidity
Path: ./contracts/crowdfund/RollingAuctionCrowdfund.sol

58:    function initialize(
59:        AuctionCrowdfundBase.AuctionCrowdfundOptions memory opts,
60:        bytes32 allowedAuctionsMerkleRoot_
61:    ) external payable onlyInitialize {	// @audit-issue

81:    function finalize(
82:        FixedGovernanceOpts memory governanceOpts,
83:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts
84:    ) external onlyDelegateCall returns (Party party_) {	// @audit-issue

105:    function finalizeOrRollOver(
106:        RollOverArgs memory args,
107:        FixedGovernanceOpts memory governanceOpts,
108:        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts
109:    ) public onlyDelegateCall returns (Party party_) {	// @audit-issue
```
*GitHub*: [61](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/RollingAuctionCrowdfund.sol#L61-L61), [84](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/RollingAuctionCrowdfund.sol#L84-L84), [109](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/RollingAuctionCrowdfund.sol#L109-L109)
```solidity
Path: ./contracts/crowdfund/AtomicManualParty.sol

40:    function createParty(
41:        Party partyImpl,
42:        Party.PartyOptions memory opts,
43:        IERC721[] memory preciousTokens,
44:        uint256[] memory preciousTokenIds,
45:        uint40 rageQuitTimestamp,
46:        address[] memory partyMembers,
47:        uint96[] memory partyMemberVotingPowers
48:    ) public returns (Party party) {	// @audit-issue

83:    function createPartyWithMetadata(
84:        Party partyImpl,
85:        Party.PartyOptions memory opts,
86:        IERC721[] memory preciousTokens,
87:        uint256[] memory preciousTokenIds,
88:        uint40 rageQuitTimestamp,
89:        MetadataProvider provider,
90:        bytes memory metadata,
91:        address[] memory partyMembers,
92:        uint96[] memory partyMemberVotingPowers
93:    ) external returns (Party party) {	// @audit-issue
```
*GitHub*: [48](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L48-L48), [93](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L93-L93)
```solidity
Path: ./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol

101:    function initialize(
102:        CollectionBatchBuyCrowdfundOptions memory opts
103:    ) external payable onlyInitialize {	// @audit-issue

134:    function batchBuy(BatchBuyArgs memory args) external onlyDelegateCall returns (Party party_) {	// @audit-issue
```
*GitHub*: [103](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L103-L103), [134](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L134-L134)
```solidity
Path: ./contracts/globals/Globals.sol

50:    function transferMultiSig(address newMultiSig) external onlyMultisig {	// @audit-issue

55:    function acceptMultiSig() external onlyPendingMultisig {	// @audit-issue
```
*GitHub*: [50](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L50-L50), [55](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/globals/Globals.sol#L55-L55)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

129:    function contractURI() external view returns (string memory) {	// @audit-issue

136:    function royaltyInfo(uint256, uint256) external view returns (address, uint256) {	// @audit-issue

146:    function getDistributionShareOf(uint256 tokenId) external view returns (uint256) {	// @audit-issue

154:    function getVotingPowerShareOf(uint256 tokenId) public view returns (uint256) {	// @audit-issue

166:    function mint(
167:        address owner,
168:        uint256 votingPower,
169:        address delegate
170:    ) external returns (uint256 tokenId) {	// @audit-issue

208:    function increaseVotingPower(uint256 tokenId, uint96 votingPower) external {	// @audit-issue

236:    function decreaseVotingPower(uint256 tokenId, uint96 votingPower) external {	// @audit-issue

247:    function increaseTotalVotingPower(uint96 votingPower) external {	// @audit-issue

255:    function decreaseTotalVotingPower(uint96 votingPower) external {	// @audit-issue

263:    function burn(uint256[] memory tokenIds) public {	// @audit-issue

310:    function burn(uint256 tokenId) external {	// @audit-issue

318:    function setRageQuit(uint40 newRageQuitTimestamp) external {	// @audit-issue

344:    function rageQuit(
345:        uint256[] calldata tokenIds,
346:        IERC20[] calldata withdrawTokens,
347:        uint256[] calldata minWithdrawAmounts,
348:        address receiver
349:    ) external {	// @audit-issue

479:    function addAuthority(address authority) external onlySelf {	// @audit-issue

486:    function abdicateAuthority() external {	// @audit-issue
```
*GitHub*: [129](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L129-L129), [136](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L136-L136), [146](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L146-L146), [154](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L154-L154), [170](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L170-L170), [208](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L208-L208), [236](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L236-L236), [247](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L247-L247), [255](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L255-L255), [263](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L263-L263), [310](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L310-L310), [318](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L318-L318), [349](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L349-L349), [479](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L479-L479), [486](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L486-L486)
```solidity
Path: ./contracts/party/Party.sol

39:    function initialize(PartyInitData memory initData) external onlyInitialize {	// @audit-issue

53:    receive() external payable {}	// @audit-issue
```
*GitHub*: [39](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/Party.sol#L39-L39), [53](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/Party.sol#L53-L53)
```solidity
Path: ./contracts/party/PartyGovernance.sol

312:    fallback() external {	// @audit-issue

333:    function supportsInterface(bytes4 interfaceId) public pure virtual returns (bool) {	// @audit-issue

342:    function getProposalExecutionEngine() external view returns (IProposalExecutionEngine) {	// @audit-issue

347:    function getProposalEngineOpts() external view returns (ProposalEngineOpts memory) {	// @audit-issue

355:    function getVotingPowerAt(
356:        address voter,
357:        uint40 timestamp
358:    ) external view returns (uint96 votingPower) {	// @audit-issue

368:    function getVotingPowerAt(
369:        address voter,
370:        uint40 timestamp,
371:        uint256 snapIndex
372:    ) public view returns (uint96 votingPower) {	// @audit-issue

381:    function getProposalStateInfo(
382:        uint256 proposalId
383:    ) external view returns (ProposalStatus status, ProposalStateValues memory values) {	// @audit-issue

390:    function getGovernanceValues() external view returns (GovernanceValues memory) {	// @audit-issue

399:    function getProposalHash(Proposal memory proposal) public pure returns (bytes32 proposalHash) {	// @audit-issue

423:    function findVotingPowerSnapshotIndex(
424:        address voter,
425:        uint40 timestamp
426:    ) public view returns (uint256 index) {	// @audit-issue

451:    function delegateVotingPower(address delegate) external {	// @audit-issue

457:    function abdicateHost(address newPartyHost) external {	// @audit-issue

487:    function distribute(
488:        uint256 amount,
489:        ITokenDistributor.TokenType tokenType,
490:        address token,
491:        uint256 tokenId
492:    ) external returns (ITokenDistributor.DistributionInfo memory distInfo) {	// @audit-issue

553:    function propose(
554:        Proposal memory proposal,
555:        uint256 latestSnapIndex
556:    ) external returns (uint256 proposalId) {	// @audit-issue

595:    function accept(uint256 proposalId, uint256 snapIndex) public returns (uint256 totalVotes) {	// @audit-issue

665:    function veto(uint256 proposalId) external {	// @audit-issue

706:    function execute(
707:        uint256 proposalId,
708:        Proposal memory proposal,
709:        IERC721[] memory preciousTokens,
710:        uint256[] memory preciousTokenIds,
711:        bytes calldata progressData,
712:        bytes calldata extraData
713:    ) external payable {	// @audit-issue

771:    function cancel(uint256 proposalId, Proposal calldata proposal) external {	// @audit-issue

841:    function emergencyExecute(
842:        address targetAddress,
843:        bytes calldata targetCallData,
844:        uint256 amountEth
845:    ) external payable onlyPartyDao onlyWhenEmergencyExecuteAllowed onlyDelegateCall {	// @audit-issue

855:    function disableEmergencyExecute() external onlyPartyDaoOrHost {	// @audit-issue
```
*GitHub*: [312](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L312-L312), [333](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L333-L333), [342](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L342-L342), [347](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L347-L347), [358](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L358-L358), [372](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L372-L372), [383](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L383-L383), [390](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L390-L390), [399](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L399-L399), [426](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L426-L426), [451](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L451-L451), [457](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L457-L457), [492](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L492-L492), [556](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L556-L556), [595](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L595-L595), [665](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L665-L665), [713](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L713-L713), [771](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L771-L771), [845](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L845-L845), [855](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L855-L855)
```solidity
Path: ./contracts/market-wrapper/NounsMarketWrapper.sol

37:    function auctionExists(uint256 auctionId) public view returns (bool) {	// @audit-issue
```
*GitHub*: [37](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/NounsMarketWrapper.sol#L37-L37)
```solidity
Path: ./contracts/market-wrapper/KoansMarketWrapper.sol

38:    function auctionExists(uint256 auctionId) public view returns (bool) {	// @audit-issue
```
*GitHub*: [38](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/KoansMarketWrapper.sol#L38-L38)
```solidity
Path: ./contracts/utils/LibRenderer.sol

28:    function calcAnimationVariables(
29:        string memory partyName
30:    ) external pure returns (uint256 duration, uint256 steps, uint256 delay, uint256 translateX) {	// @audit-issue

42:    function formatAsDecimalString(
43:        uint256 n,
44:        uint256 decimals,
45:        uint256 maxChars
46:    ) external pure returns (string memory) {	// @audit-issue

64:    function prependNumWithZeros(
65:        string memory numStr,
66:        uint256 expectedLength
67:    ) public pure returns (string memory) {	// @audit-issue

78:    function generateColorHex(
79:        Color color,
80:        ColorType colorType
81:    ) external pure returns (string memory colorHex) {	// @audit-issue

165:    function getCollectionImageAndBanner(
166:        Color color,
167:        bool isDarkMode
168:    ) external pure returns (string memory image, string memory banner) {	// @audit-issue
```
*GitHub*: [30](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L30-L30), [46](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L46-L46), [67](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L67-L67), [81](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L81-L81), [168](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/LibRenderer.sol#L168-L168)
```solidity
Path: ./contracts/utils/Implementation.sol

40:    function IMPL() external view returns (address) {	// @audit-issue
```
*GitHub*: [40](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/Implementation.sol#L40-L40)
```solidity
Path: ./contracts/utils/ReadOnlyDelegateCall.sol

16:    function delegateCallAndRevert(address impl, bytes memory callData) external {	// @audit-issue
```
*GitHub*: [16](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/ReadOnlyDelegateCall.sol#L16-L16)
```solidity
Path: ./contracts/utils/Multicall.sol

10:    function multicall(bytes[] calldata multicallData) external {	// @audit-issue
```
*GitHub*: [10](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/Multicall.sol#L10-L10)
```solidity
Path: ./contracts/utils/EIP165.sol

9:    function supportsInterface(bytes4 interfaceId) public pure virtual returns (bool) {	// @audit-issue
```
*GitHub*: [9](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/EIP165.sol#L9-L9)
```solidity
Path: ./contracts/utils/PartyHelpers.sol

39:    function getCurrentDelegates(
40:        Party party,
41:        address[] calldata members
42:    ) external view returns (MemberAndDelegate[] memory membersAndDelegates) {	// @audit-issue

53:    function getVotingPowersAt(
54:        Party party,
55:        address[] calldata voters,
56:        uint40 timestamp,
57:        uint256[] calldata indexes
58:    ) external view returns (MemberAndVotingPower[] memory memberAndVotingPower) {	// @audit-issue

73:    function getNftInfos(
74:        Party party,
75:        uint256 startTokenId,
76:        uint256 endTokenId
77:    ) external view returns (NftInfo[] memory nftInfos) {	// @audit-issue

101:    function getNftInfosBatch(
102:        Party party,
103:        uint256[] memory tokenIds
104:    ) external view returns (NftInfo[] memory nftInfos) {	// @audit-issue

120:    function getRageQuitWithdrawAmounts(
121:        Party party,
122:        uint256[] calldata tokenIds,
123:        IERC20[] calldata withdrawTokens
124:    ) external view returns (uint256[] memory withdrawAmounts) {	// @audit-issue
```
*GitHub*: [42](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/PartyHelpers.sol#L42-L42), [58](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/PartyHelpers.sol#L58-L58), [77](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/PartyHelpers.sol#L77-L77), [104](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/PartyHelpers.sol#L104-L104), [124](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/PartyHelpers.sol#L124-L124)
```solidity
Path: ./contracts/gatekeepers/AllowListGateKeeper.sol

42:    function createGate(bytes32 merkleRoot) external returns (bytes12 id) {	// @audit-issue
```
*GitHub*: [42](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/gatekeepers/AllowListGateKeeper.sol#L42-L42)
```solidity
Path: ./contracts/gatekeepers/TokenGateKeeper.sol

54:    function createGate(Token token, uint256 minimumBalance) external returns (bytes12 id) {	// @audit-issue
```
*GitHub*: [54](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/gatekeepers/TokenGateKeeper.sol#L54-L54)
```solidity
Path: ./contracts/operators/ERC20SwapOperator.sol

95:    function setTargetAllowed(address target, bool isAllowed) external onlyPartyDao {	// @audit-issue

211:    receive() external payable {}	// @audit-issue
```
*GitHub*: [95](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L95-L95), [211](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L211-L211)
```solidity
Path: ./contracts/operators/CollectionBatchBuyOperator.sol

243:    receive() external payable {}	// @audit-issue
```
*GitHub*: [243](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/CollectionBatchBuyOperator.sol#L243-L243)
```solidity
Path: ./contracts/vendor/solmate/ERC20.sol

106:    function permit(
107:        address owner,
108:        address spender,
109:        uint256 value,
110:        uint256 deadline,
111:        uint8 v,
112:        bytes32 r,
113:        bytes32 s
114:    ) public virtual {	// @audit-issue

152:    function DOMAIN_SEPARATOR() public view virtual returns (bytes32) {	// @audit-issue
```
*GitHub*: [114](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC20.sol#L114-L114), [152](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC20.sol#L152-L152)
```solidity
Path: ./contracts/vendor/solmate/ERC1155.sol

30:    function uri(uint256 id) public view virtual returns (string memory);	// @audit-issue

136:    function supportsInterface(bytes4 interfaceId) public view virtual returns (bool) {	// @audit-issue

234:    function onERC1155Received(
235:        address,
236:        address,
237:        uint256,
238:        uint256,
239:        bytes calldata
240:    ) external virtual returns (bytes4) {	// @audit-issue

244:    function onERC1155BatchReceived(
245:        address,
246:        address,
247:        uint256[] calldata,
248:        uint256[] calldata,
249:        bytes calldata
250:    ) external virtual returns (bytes4) {	// @audit-issue
```
*GitHub*: [30](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L30-L30), [136](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L136-L136), [240](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L240-L240), [250](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC1155.sol#L250-L250)
```solidity
Path: ./contracts/vendor/solmate/ERC721.sol

22:    function tokenURI(uint256 id /* view */) public virtual returns (string memory);	// @audit-issue

32:    function ownerOf(uint256 id) public view virtual returns (address owner) {	// @audit-issue

36:    function balanceOf(address owner) public view virtual returns (uint256) {	// @audit-issue

63:    function approve(address spender, uint256 id) public virtual {	// @audit-issue

73:    function setApprovalForAll(address operator, bool approved) public virtual {	// @audit-issue

79:    function transferFrom(address from, address to, uint256 id) public virtual {	// @audit-issue

106:    function safeTransferFrom(address from, address to, uint256 id) public virtual {	// @audit-issue

117:    function safeTransferFrom(
118:        address from,
119:        address to,
120:        uint256 id,
121:        bytes calldata data
122:    ) public virtual {	// @audit-issue

211:    function onERC721Received(
212:        address,
213:        address,
214:        uint256,
215:        bytes calldata
216:    ) external virtual returns (bytes4) {	// @audit-issue
```
*GitHub*: [22](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L22-L22), [32](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L32-L32), [36](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L36-L36), [63](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L63-L63), [73](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L73-L73), [79](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L79-L79), [106](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L106-L106), [122](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L122-L122), [216](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L216-L216)
```solidity
Path: ./contracts/authorities/AddPartyCardsAuthority.sol

29:    function addPartyCards(
30:        address[] calldata newPartyMembers,
31:        uint96[] calldata newPartyMemberVotingPowers,
32:        address[] calldata initialDelegates
33:    ) external {	// @audit-issue
```
*GitHub*: [33](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/authorities/AddPartyCardsAuthority.sol#L33-L33)
```solidity
Path: ./contracts/signature-validators/OffChainSignatureValidator.sol

28:    function isValidSignature(bytes32 hash, bytes memory signature) external view returns (bytes4) {	// @audit-issue

84:    function setSigningThresholdBps(uint96 thresholdBps) external {	// @audit-issue
```
*GitHub*: [28](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L28-L28), [84](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/signature-validators/OffChainSignatureValidator.sol#L84-L84)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

307:    function emergencyExecute(
308:        address targetAddress,
309:        bytes calldata targetCallData
310:    ) external onlyPartyDao onlyIfEmergencyActionsAllowed {	// @audit-issue
```
*GitHub*: [310](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L310-L310)
</details>



### [N-24] Polymorphic functions make security audits more time-consuming and error-prone
### Severity
* Impact: Low
* Likelihood: Low
### Description

The instances below point to one of two functions with the same name. Consider naming each function differently, in order to make code navigation and analysis easier.

### Number Of Instances Found
11

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/InitialETHCrowdfund.sol

185:    function contribute(	// @audit-issue same function also on line(s): 164
```
*GitHub*: [185](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/InitialETHCrowdfund.sol#L185-L185)
```solidity
Path: ./contracts/crowdfund/CrowdfundNFT.sol

66:    function safeTransferFrom(	// @audit-issue same function also on line(s): 62
```
*GitHub*: [66](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/CrowdfundNFT.sol#L66-L66)
```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

537:    function _createParty(	// @audit-issue same function also on line(s): 483
```
*GitHub*: [537](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L537-L537)
```solidity
Path: ./contracts/crowdfund/AuctionCrowdfundBase.sol

216:    function bid(	// @audit-issue same function also on line(s): 168, 187
```
*GitHub*: [216](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AuctionCrowdfundBase.sol#L216-L216)
```solidity
Path: ./contracts/crowdfund/BuyCrowdfundBase.sol

168:    function _finalize(	// @audit-issue same function also on line(s): 128
```
*GitHub*: [168](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/BuyCrowdfundBase.sol#L168-L168)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

310:    function burn(uint256 tokenId) external {	// @audit-issue same function also on line(s): 263

465:    function safeTransferFrom(	// @audit-issue same function also on line(s): 458
```
*GitHub*: [310](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L310-L310), [465](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L465-L465)
```solidity
Path: ./contracts/party/PartyGovernance.sol

368:    function getVotingPowerAt(	// @audit-issue same function also on line(s): 355
```
*GitHub*: [368](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L368-L368)
```solidity
Path: ./contracts/utils/vendor/Strings.sol

68:    function toHexString(address addr) internal pure returns (string memory) {	// @audit-issue same function also on line(s): 37, 53
```
*GitHub*: [68](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/utils/vendor/Strings.sol#L68-L68)
```solidity
Path: ./contracts/vendor/solmate/ERC721.sol

117:    function safeTransferFrom(	// @audit-issue same function also on line(s): 106

196:    function _safeMint(address to, uint256 id, bytes memory data) internal virtual {	// @audit-issue same function also on line(s): 185
```
*GitHub*: [117](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L117-L117), [196](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC721.sol#L196-L196)
</details>



### [N-25] Event is not properly `indexed`
### Severity
* Impact: Low
* Likelihood: Low
### Description

Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. This is especially useful when it comes to filtering based on an address. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Where applicable, each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three applicable fields, all of the applicable fields should be `indexed`.

### Number Of Instances Found
27

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/crowdfund/Crowdfund.sol

115:    event Burned(address contributor, uint256 ethUsed, uint256 ethOwed, uint256 votingPower);	// @audit-issue

116:    event Contributed(	// @audit-issue

123:    event EmergencyExecute(address target, bytes data, uint256 amountEth);	// @audit-issue
```
*GitHub*: [115](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L115-L115), [116](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L116-L116), [123](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/Crowdfund.sol#L123-L123)
```solidity
Path: ./contracts/crowdfund/ETHCrowdfundBase.sol

75:    event Contributed(	// @audit-issue

84:    event EmergencyExecute(address target, bytes data, uint256 amountEth);	// @audit-issue
```
*GitHub*: [75](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L75-L75), [84](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/ETHCrowdfundBase.sol#L84-L84)
```solidity
Path: ./contracts/crowdfund/AtomicManualParty.sol

14:    event AtomicManualPartyCreated(	// @audit-issue
```
*GitHub*: [14](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/crowdfund/AtomicManualParty.sol#L14-L14)
```solidity
Path: ./contracts/party/PartyGovernanceNFT.sol

30:    event Burn(address caller, uint256 tokenId, uint256 votingPower);	// @audit-issue

31:    event RageQuit(address caller, uint256[] tokenIds, IERC20[] withdrawTokens, address receiver);	// @audit-issue
```
*GitHub*: [30](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L30-L30), [31](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernanceNFT.sol#L31-L31)
```solidity
Path: ./contracts/party/IPartyFactory.sol

10:    event PartyCreated(	// @audit-issue
```
*GitHub*: [10](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/IPartyFactory.sol#L10-L10)
```solidity
Path: ./contracts/party/PartyGovernance.sol

144:    event Proposed(uint256 proposalId, address proposer, Proposal proposal);	// @audit-issue

145:    event ProposalAccepted(uint256 proposalId, address voter, uint256 weight);	// @audit-issue

146:    event EmergencyExecute(address target, bytes data, uint256 amountEth);	// @audit-issue

149:    event ProposalVetoed(uint256 indexed proposalId, address host);	// @audit-issue

150:    event ProposalExecuted(uint256 indexed proposalId, address executor, bytes nextProgressData);	// @audit-issue

152:    event DistributionCreated(	// @audit-issue

158:    event HostStatusTransferred(address oldHost, address newHost);	// @audit-issue
```
*GitHub*: [144](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L144-L144), [145](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L145-L145), [146](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L146-L146), [149](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L149-L149), [150](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L150-L150), [152](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L152-L152), [158](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/party/PartyGovernance.sol#L158-L158)
```solidity
Path: ./contracts/proposals/ListOnOpenseaAdvancedProposal.sol

92:    event OpenseaAdvancedOrderListed(	// @audit-issue

102:    event OpenseaAdvancedOrderSold(	// @audit-issue

109:    event OpenseaOrderExpired(bytes32 orderHash, address token, uint256 tokenId, uint256 expiry);	// @audit-issue
```
*GitHub*: [92](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L92-L92), [102](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L102-L102), [109](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/ListOnOpenseaAdvancedProposal.sol#L109-L109)
```solidity
Path: ./contracts/proposals/OperatorProposal.sol

43:    event OperationExecuted(address executor);	// @audit-issue
```
*GitHub*: [43](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/proposals/OperatorProposal.sol#L43-L43)
```solidity
Path: ./contracts/operators/ERC20SwapOperator.sol

33:    event TargetAllowedSet(address target, bool isAllowed);	// @audit-issue
```
*GitHub*: [33](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/operators/ERC20SwapOperator.sol#L33-L33)
```solidity
Path: ./contracts/vendor/markets/INounsAuctionHouse.sol

40:    event AuctionBid(uint256 indexed nounId, address sender, uint256 value, bool extended);	// @audit-issue

44:    event AuctionSettled(uint256 indexed nounId, address winner, uint256 amount);	// @audit-issue
```
*GitHub*: [40](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/markets/INounsAuctionHouse.sol#L40-L40), [44](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/markets/INounsAuctionHouse.sol#L44-L44)
```solidity
Path: ./contracts/vendor/markets/IKoansAuctionHouse.sol

24:    event AuctionBid(uint256 indexed koanId, address sender, uint256 value, bool extended);	// @audit-issue

28:    event AuctionSettled(uint256 indexed koanId, address winner, uint256 amount);	// @audit-issue
```
*GitHub*: [24](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/markets/IKoansAuctionHouse.sol#L24-L24), [28](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/markets/IKoansAuctionHouse.sol#L28-L28)
```solidity
Path: ./contracts/distribution/ITokenDistributor.sol

36:    event DistributionFeeClaimed(	// @audit-issue
```
*GitHub*: [36](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/ITokenDistributor.sol#L36-L36)
```solidity
Path: ./contracts/distribution/TokenDistributor.sol

46:    event EmergencyExecute(address target, bytes data);	// @audit-issue
```
*GitHub*: [46](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/distribution/TokenDistributor.sol#L46-L46)
</details>



### [N-26] Interfaces should be indicated with an `I` prefix in the contract name
### Severity
* Impact: Low
* Likelihood: Low
### Description


### Number Of Instances Found
3

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/gatekeepers/TokenGateKeeper.sol

10:interface Token {	// @audit-issue
```
*GitHub*: [10](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/gatekeepers/TokenGateKeeper.sol#L10-L10)
```solidity
Path: ./contracts/vendor/markets/IReserveAuctionCoreEth.sol

77:interface ZoraModuleManager {	// @audit-issue

81:interface BaseTransferHelper {	// @audit-issue
```
*GitHub*: [77](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/markets/IReserveAuctionCoreEth.sol#L77-L77), [81](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/markets/IReserveAuctionCoreEth.sol#L81-L81)
</details>



### [N-27] Use `abi.encodeCall()` instead of `abi.encodeWithSignature()`/`abi.encodeWithSelector()`
### Severity
* Impact: Low
* Likelihood: Low
### Description

Starting with version 0.8.11, abi.encodeCall() has compiler [type safety](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3693), whereas the other two functions do not.

### Number Of Instances Found
2

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/market-wrapper/NounsMarketWrapper.sol

92:            abi.encodeWithSignature("createBid(uint256)", auctionId)	// @audit-issue
```
*GitHub*: [92](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/NounsMarketWrapper.sol#L92-L92)
```solidity
Path: ./contracts/market-wrapper/KoansMarketWrapper.sol

93:            abi.encodeWithSignature("createBid(uint256)", auctionId)	// @audit-issue
```
*GitHub*: [93](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/market-wrapper/KoansMarketWrapper.sol#L93-L93)
</details>



### [N-28] Consider implementing EIP-5267 to securely describe EIP-712 domains being used
### Severity
* Impact: Low
* Likelihood: Low
### Description

[EIP-5267](https://eips.ethereum.org/EIPS/eip-5267) is a standard which allows for the retrieval and description of EIP-712 hash domains. This allows external tools to allow users to not just view the struct fields being signed, but the fields the domain the signature contains as well. This is especially useful when a project may exist on multiple chains and or in multiple contracts, and allows users/tools to verify that the signature is for the right fork, chain, version, contract, etc.

### Number Of Instances Found
1

### Code Location
<details><summary>Click to show findings</summary>

```solidity
Path: ./contracts/vendor/solmate/ERC20.sol

158:        return
159:            keccak256(
160:                abi.encode(
161:                    keccak256(
162:                        "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"	// @audit-issue
163:                    ),
164:                    keccak256(bytes(name)),
165:                    keccak256("1"),
166:                    block.chainid,
167:                    address(this)
168:                )
169:            );
```
*GitHub*: [162](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/./contracts/vendor/solmate/ERC20.sol#L162-L162)
</details>



