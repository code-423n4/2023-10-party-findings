# G-1. Use assembly in place of abi.decode to extract calldata values more efficiently

Instead of using abi.decode, we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use.

reference : https://code4rena.com/reports/2023-08-goodentry#g-02-use-assembly-in-place-of-abidecode-to-extract-calldata-values-more-efficiently

Instances : 
```
File : contracts/crowdfund/InitialETHCrowdfund.sol

367 :             amounts[i] = abi.decode(r, (uint96));
```

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L367
```
File : contracts/party/PartyGovernance.sol

892 :             nextProgressData = abi.decode(resultData, (bytes));
```
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L892
```
File : contracts/proposals/ProposalExecutionEngine.sol

!33 :         ProposalEngineOpts memory opts = abi.decode(initializeData, (ProposalEngineOpts));
```
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/proposals/ProposalExecutionEngine.sol#L133


# G-2. Use constants instead of type(uintx).max

type(uint120).max or type(uint112).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.

Reference : https://code4rena.com/reports/2023-08-goodentry#g-20-use-constants-instead-of-typeuintxmax
```
File : contracts/party/PartyGovernance.sol

187 :     uint96 private constant VETO_VALUE = type(uint96).max;

302 :         if (govOpts.hosts.length > type(uint8).max) {

359 :         return getVotingPowerAt(voter, timestamp, type(uint256).max);

445 :         return high == 0 ? type(uint256).max : high - 1;

926 :             if (hintIndex != type(uint256).max) {

1083 :         if (pv.votes == type(uint96).max) {
```

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L187

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L302

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L359

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L445

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L926

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1083

```
File : contracts/proposals/ProposalExecutionEngine.sol

185 :             stor.nextProgressDataHash = bytes32(type(uint256).max);

314 :         require(uint8(proposalType) <= uint8(type(ProposalType).max));
```
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/proposals/ProposalExecutionEngine.sol#L185

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/proposals/ProposalExecutionEngine.sol#L314

# G-3. Avoid emitting event on every iteration

Expensive operations should always try to be avoided within loops. Such operations include: reading/writing to storage, heavy calculations, external calls, and emitting events. In this instance, an event is being emitted every iteration. Events have a base cost of `Glog (375 gas)` per emit and `Glogdata (8 gas) * number of bytes in event`. We can avoid incurring those costs each iteration by emitting the event outside of the loop.
```
File : contracts/crowdfund/InitialETHCrowdfund.sol

341 :             emit Refunded(contributor, tokenId, amount);
```
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L341


# G-4. Use hardcoded address instead of address(this)

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.  

Reference : https://code4rena.com/reports/2023-08-goodentry#g-10-use-hardcoded-address-instead-of-addressthis
```
File : contracts/crowdfund/InitialETHCrowdfund.sol

382 :         authorities[authoritiesLength - 1] = address(this);
```
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L382
```
File : contracts/party/PartyGovernanceNFT.sol

387 :                     ? address(this).balance

388 :                     : withdrawTokens[i].balanceOf(address(this));
```
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L387

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L388

# G-5. Use abi.encodePacked() not abi.encode()

`abi.encode` pads extra null bytes at the end of the call data which is normally unnecessary. In general, `abi.encodePacked` is more gas-efficient.
```
File : contracts/party/PartyGovernance.sol

286 :             abi.encode(proposalEngineOpts)
```
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L286

