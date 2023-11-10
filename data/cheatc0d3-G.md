G-1: Use calldata instead of Memory to save gas

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L108

Original code:

```solidity
function initialize(
        InitialETHCrowdfundOptions memory crowdfundOpts,
        ETHPartyOptions memory partyOpts,
        MetadataProvider customMetadataProvider,
        bytes memory customMetadata
    ) external payable onlyInitialize {
```

Optimized code:

```solidity
function initialize(
    InitialETHCrowdfundOptions calldata crowdfundOpts,
    ETHPartyOptions calldata partyOpts,
    MetadataProvider customMetadataProvider,
    bytes calldata customMetadata
) external payable onlyInitialize {

```
Since none of these parameters are modified within the function, then you could potentially declare crowdfundOpts, partyOpts, and customMetadata as calldata to potentially save on gas costs.

G-2: Potential optimization in contributeFor function 

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L235

Original code:
```solidity
function contributeFor(
        uint256 tokenId,
        address payable recipient,
        address initialDelegate,
        bytes memory gateData
```

Optimized code:

```solidity
bytes calldata gateData
```

Since contributeFor is an external function and gateData is not modified, you could declare gateData as calldata to potentially save on gas.

G-3: Potential optimization in propose function

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L553

Original:
```solidity
function propose(
        Proposal memory proposal,
        uint256 latestSnapIndex
```

Optimized:
```solidity
Proposal calldata proposal
```
Change the proposal parameter to use calldata instead of memory to save on gas costs.

