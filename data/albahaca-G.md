# GAS OPTIMIZATION

# SUMMARY
|      |  issue  |  instance  |
|------|---------|------------|
|[G‑01]|Pre-increment and pre-decrement are cheaper than +1 ,-1|2|
|[G‑02]|Optimize External Calls with Assembly for Memory Efficiency|11|
|[G‑03]|Avoid contract existence checks by using low level calls|2|
|[G‑04]|Can Make The Variable Outside The Loop To Save Gas |5|
|[G‑05]|Access mappings directly rather than using accessor functions|1|
|[G‑06]|Make 3 event parameters indexed when possible|21|
|[G‑07]|Expressions for constant values such as a call to keccak256(), should use immutable rather than constant|3|
|[G‑08]|abi.encode() is less efficient than abi.encodePacked()|1|
|[G‑09]|Delete variables that you don’t need|1|
|[G‑10]|With assembly, .call (bool success) transfer can be done gas-optimized|3|
|[G‑11]|Use hardcode address instead address(this)|6|
|[G‑12]|Structs can be packed to use fewer storage slots|2|
|[G‑13]|Use constants instead of type(uintx).max|13|
|[G‑14]|Not using the named return variables when a function returns, wastes deployment gas|14|
|[G‑15]|Use ERC721A instead ERC721|~|
|[G‑16]|subtractions can be unchecked to save gas|1|
|[G‑17]|Use assembly for math (add, sub, mul, div)|~|
|[G‑18]|Using a positive conditional flow to save a NOT opcode|1|
|[G‑19]|Do-While loops are cheaper than for loops|1|
|[G‑20]|decrement should be unchecked to save gas|1|
|[G‑21]|Using Fixed Bytes Is Cheaper Than Using String|4|
|[G‑22]|avoid emitting empty data to save gas|3|
|[G‑23]|Massive 15k per tx gas savings - use 1 and 2|1|
|[G‑24]|Use Modifiers Instead of Functions To Save Gas|4|
|[G‑25]|Use assembly in place of abi.decode to extract calldata values more efficiently|5|
|[G‑26]|Combine multiple for loop|1|
|[G‑27]|Ternary operation is cheaper than if-else statement|4|
|[G‑28]|Unlimited gas consumption risk due to external call recipients|2|
|[G‑29]|Using assembly to revert with an error message|~|
|[G‑30]|Split revert statements|2|
|[G‑31]|Write gas-optimal for-loops|~|
|[G‑32]|Avoid updating storage when the value hasn't changed|7|









## [G-01] Pre-increment and pre-decrement are cheaper than +1 ,-1

```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
377   uint256 authoritiesLength = opts.authorities.length + 1;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L377

There are 2 instances of this issue:
```solidity
File:  contracts/party/PartyGovernance.sol
440   low = mid + 1;

445   return high == 0 ? type(uint256).max : high - 1;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L440


## [G-02] Optimize External Calls with Assembly for Memory Efficiency
Using interfaces to make external contract calls in Solidity is convenient but can be inefficient in terms of memory utilization. Each such call involves creating a new memory location to store the data being passed, thus incurring memory expansion costs.

Inline assembly allows for optimized memory usage by re-using already allocated memory spaces or using the scratch space for smaller datasets. This can result in notable gas savings, especially for contracts that make frequent external calls.

Additionally, using inline assembly enables important safety checks like verifying if the target address has code deployed to it using extcodesize(addr) before making the call, mitigating risks associated with contract interactions.

There are 11 instances of this issue:
```solidity
File:  contracts/crowdfund/ETHCrowdfundBase.sol
372   if (_GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET) != msg.sender) {

392   _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET) != msg.sender    
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L392


```solidity
File:  contracts/party/PartyGovernance.sol
233  address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);

243  address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);

259  if (_GLOBALS.getBool(LibGlobals.GLOBAL_DISABLE_PARTY_ACTIONS)) {

534  IERC20(token).compatTransfer(address(distributor), amount);

791  uint256 globalMaxCancelDelay = _GLOBALS.getUint256(
                LibGlobals.GLOBAL_PROPOSAL_MAX_CANCEL_DURATION
            );
            uint256 globalMinCancelDelay = _GLOBALS.getUint256(
                LibGlobals.GLOBAL_PROPOSAL_MIN_CANCEL_DURATION
            );
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L233



```solidity
File: contracts/party/PartyGovernanceNFT.sol
107              RendererStorage(_GLOBALS.getAddress(LibGlobals.GLOBAL_RENDERER_STORAGE))
                .useCustomizationPreset(customizationPresetId);

422  token.compatTransfer(feeRecipient, fee);

438  token.compatTransfer(receiver, amount);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L107

## [G‑03] Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence.

There are 2 instances of this issue:


```solidity
File: contracts/party/PartyGovernanceNFT.sol
107              RendererStorage(_GLOBALS.getAddress(LibGlobals.GLOBAL_RENDERER_STORAGE))
                .useCustomizationPreset(customizationPresetId);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L107

```solidity
File:  contracts/party/PartyGovernance.sol
534  IERC20(token).compatTransfer(address(distributor), amount);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L233


## [G-04] Can Make The Variable Outside The Loop To Save Gas 
When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements.

By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract. Here's an example:

```
contract MyContract {
    function sum(uint256[] memory values) public pure returns (uint256) {
        uint256 total = 0;
        
        for (uint256 i = 0; i < values.length; i++) {
            total += values[i];
        }
        
        return total;
    }
}
```

There are 5 instances of this issue:

```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
358   (bool s, bytes memory r) = address(this).call(
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L358


```solidity
File: contracts/party/PartyGovernanceNFT.sol
273            uint256 tokenId = tokenIds[i];
274            address owner = ownerOf(tokenId);

386   uint256 balance = address(withdrawTokens[i]) == ETH_ADDRESS

410                uint256 amount = withdrawAmounts[i];

413                uint256 fee = (amount * feeBps_) / 1e4;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L273

## [G-05] Access mappings directly rather than using accessor functions
Saves having to do two JUMP instructions, along with stack setup

There are 1 instances of this issue:


```solidity
File: contracts/party/PartyGovernanceNFT.sol
147   return votingPowerByTokenId[tokenId];
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L147

## [G-06] Make 3 event parameters indexed when possible
It is the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

There are 21 instances of this issue:

```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
    event Contributed(
        address indexed sender,
        address indexed contributor,
        uint256 amount,
        address delegate
    );

    event FundingSplitSent(address indexed fundingSplitRecipient, uint256 amount);

    event EmergencyExecute(address target, bytes data, uint256 amountEth);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L75-L84


```solidity
File:  contracts/crowdfund/InitialETHCrowdfund.sol
96   event Refunded(address indexed contributor, uint256 indexed tokenId, uint256 amount);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L96


```solidity
File: contracts/party/PartyGovernance.sol
144 event Proposed(uint256 proposalId, address proposer, Proposal proposal);
    event ProposalAccepted(uint256 proposalId, address voter, uint256 weight);
    event EmergencyExecute(address target, bytes data, uint256 amountEth);

149 event ProposalVetoed(uint256 indexed proposalId, address host);
    event ProposalExecuted(uint256 indexed proposalId, address executor, bytes nextProgressData);  

152 event DistributionCreated(
        ITokenDistributor.TokenType tokenType,
        address token,
        uint256 tokenId
    );     

158 event HostStatusTransferred(address oldHost, address newHost);

160 event PartyVotingSnapshotCreated(
        address indexed voter,
        uint40 timestamp,
        uint96 delegatedVotingPower,
        uint96 intrinsicVotingPower,
        bool isDelegated
    );
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L144


```solidity
File: contracts/party/PartyGovernanceNFT.sol
29  event RageQuitSet(uint40 oldRageQuitTimestamp, uint40 newRageQuitTimestamp);
    event Burn(address caller, uint256 tokenId, uint256 votingPower);
    event RageQuit(address caller, uint256[] tokenIds, IERC20[] withdrawTokens, address receiver);
    event PartyCardIntrinsicVotingPowerSet(uint256 indexed tokenId, uint256 intrinsicVotingPower);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L29-L32


```solidity
File: contracts/proposals/ProposalExecutionEngine.sol
83   event ProposalEngineImplementationUpgraded(address oldImpl, address newImpl);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L83



```solidity
File: contracts/proposals/SetGovernanceParameterProposal.sol
    event VoteDurationSet(uint256 oldValue, uint256 newValue);
    /// @notice Emitted when the execution delay is set
    event ExecutionDelaySet(uint256 oldValue, uint256 newValue);
    /// @notice Emitted when the pass threshold bps is set
    event PassThresholdBpsSet(uint256 oldValue, uint256 newValue);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol#L11-L15


```solidity
File: contracts/signature-validators/OffChainSignatureValidator.sol
15  event SigningThresholdBpsSet(
        Party indexed party,
        uint96 oldThresholdBps,
        uint96 newThresholdBps
    );
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L15

## [G-07] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant
The reason for this is that constant variables are evaluated at runtime and their value is included in the bytecode of the contract. This means that any expensive operations performed as part of the constant expression, such as a call to keccak256(), will be executed every time the contract is deployed, even if the result is always the same. This can result in higher gas costs.

```
bytes32 constant MY_HASH = keccak256("my string");
```
Alternatively, we could use an immutable variable, like so:
```
bytes32 immutable MY_HASH = keccak256("my string");
```

There are 3 instances of this issue:

```solidity
File:  contracts/proposals/ProposalExecutionEngine.sol
104   uint256 private constant _STORAGE_SLOT = uint256(keccak256("ProposalExecutionEngine.Storage"));
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L104



```solidity
File: contracts/proposals/ProposalStorage.sol
40   uint256 private constant SHARED_STORAGE_SLOT =
        uint256(keccak256("ProposalStorage.SharedProposalStorage"));
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L40-L41


```solidity
File: contracts/proposals/SetSignatureValidatorProposal.sol
13    uint256 private constant _SET_SIGNATURE_VALIDATOR_PROPOSAL_STORAGE_SLOT =
        uint256(keccak256("SetSignatureValidatorProposal.Storage"));
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L13-L14


## [G-08] abi.encode() is less efficient than abi.encodePacked()
In terms of efficiency, abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost of reduced safety, as abi.encodePacked() does not perform any type checking or padding of data.

There are 1 instances of this issue:

```solidity
File: contracts/party/PartyGovernance.sol
386  abi.encode(proposalEngineOpts)
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L386


## [G-09] Delete variables that you don’t need
In Ethereum, you get a gas refund for freeing up storage space.
Deleting a variable refund 15,000 gas up to a maximum of half the gas cost of the transaction. Deleting with the delete keyword is equivalent to assigning the initial value for the data type, such as 0 for integers.

There are 1 instances of this issue:
```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
319  delete expiry;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L319



## [G-10] With assembly, .call (bool success) transfer can be done gas-optimized
return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.
https://code4rena.com/reports/2023-01-biconomy#g-01-with-assembly-call-bool-success-transfer-can-be-done-gas-optimized

There are 3 instances of this issue:
```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
379   (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L379


```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
358  (bool s, bytes memory r) = address(this).call(
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L358



```solidity
File: contracts/party/PartyGovernance.sol
846  (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L846

## [G-11] Use hardcode address instead address(this)
it can be more gas-efficient to use a hardcoded address instead of the address(this) expression, especially if you need to use the same address multiple times in your contract.

The reason for this is that using address(this) requires an additional EXTCODESIZE operation to retrieve the contract's address from its bytecode, which can increase the gas cost of your contract. By pre-calculating and using a hardcoded address, you can avoid this additional operation and reduce the overall gas cost of your contract.

Here's an example of how you can use a hardcoded address instead of address(this):

```
contract MyContract {
    address public myAddress = 0x1234567890123456789012345678901234567890;
    #L
    function doSomething() public {
        // Use myAddress instead of address(this)
        require(msg.sender == myAddress, "Caller is not authorized");
        
        // Do something
    }
}
```
In the above example, we have a contract MyContract with a public address variable myAddress. Instead of using address(this) to retrieve the contract's address, we have pre-calculated and hardcoded the address in the variable. This can help to reduce the gas cost of our contract and make our code more efficient.

[References](https://book.getfoundry.sh/reference/forge-std/compute-create-address)

There are 6 instances of this issue:


```solidity
File: contracts/party/PartyGovernance.sol
496   if (msg.sender != address(this)) {

527   Party(payable(address(this))),

538   Party(payable(address(this))),
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L496



```solidity
File: contracts/party/PartyGovernanceNFT.sol
68  if (msg.sender != address(this)) {

387 ? address(this).balance

388 : withdrawTokens[i].balanceOf(address(this));
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L68

## [G-12] Structs can be packed to use fewer storage slots
The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to each other in storage and this will pack the values together into a single 32 byte storage slot (if values combined are <= 32 bytes). If the variables packed together are retrieved together in functions (more likely with structs) we will effectively save ~2000 gas with every subsequent SLOAD for that storage slot. This is due to us incurring a Gwarmaccess (100 gas) versus a Gcoldsload (2100 gas).

There are 2 instances of this issue:
```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
    struct ETHCrowdfundOptions {
        Party party;
        address payable initialContributor;
        address initialDelegate;
        uint96 minContribution;
        uint96 maxContribution;
        bool disableContributingForExistingCard;
        uint96 minTotalContributions;
        uint96 maxTotalContributions;
        uint16 exchangeRateBps;
        uint16 fundingSplitBps;
        address payable fundingSplitRecipient;
        uint40 duration;
        IGateKeeper gateKeeper;
        bytes12 gateKeeperId;
    }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L33-L48

`Fix code` and save 1 storage slot

```solidity
    struct ETHCrowdfundOptions {
        address payable initialContributor;
        uint96 minContribution;
        address initialDelegate;
        uint96 maxContribution;
        address payable fundingSplitRecipient;
        uint96 minTotalContributions;
        uint96 maxTotalContributions;
        uint16 exchangeRateBps;
        uint16 fundingSplitBps;
        bool disableContributingForExistingCard;
        uint40 duration;
        IGateKeeper gateKeeper;
        Party party;
        bytes12 gateKeeperId;
    }
```

```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
    struct InitialETHCrowdfundOptions {
        address payable initialContributor;
        address initialDelegate;
        uint96 minContribution;
        uint96 maxContribution;
        bool disableContributingForExistingCard;
        uint96 minTotalContributions;
        uint96 maxTotalContributions;
        uint16 exchangeRateBps;
        uint16 fundingSplitBps;
        address payable fundingSplitRecipient;
        uint40 duration;
        IGateKeeper gateKeeper;
        bytes12 gateKeeperId;
    }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L26-L40

`Fix code`:
```solidity
    struct InitialETHCrowdfundOptions {
        address payable initialContributor;
        uint96 minContribution;
        address initialDelegate;
        uint96 maxContribution;
        address payable fundingSplitRecipient;
        uint96 minTotalContributions;
        uint96 maxTotalContributions;
        uint16 exchangeRateBps;
        uint16 fundingSplitBps;
        bool disableContributingForExistingCard; 
        uint40 duration;
        IGateKeeper gateKeeper;
        bytes12 gateKeeperId;
    }

```

## [G-13] Use constants instead of type(uintx).max
Using constants instead of type(uintx).max can save gas in some cases because constants are precomputed at compile-time and can be reused throughout the code, whereas type(uintx).max requires computation at runtime

There are 13 instances of this issue:
```solidity
File: contracts/party/PartyGovernance.sol
359  return getVotingPowerAt(voter, timestamp, type(uint256).max);

445  return high == 0 ? type(uint256).max : high - 1;

520  emit BatchMetadataUpdate(0, type(uint256).max);

581  emit BatchMetadataUpdate(0, type(uint256).max);

655  emit BatchMetadataUpdate(0, type(uint256).max);

688  emit BatchMetadataUpdate(0, type(uint256).max);

833  emit BatchMetadataUpdate(0, type(uint256).max);

897  emit BatchMetadataUpdate(0, type(uint256).max);

926  if (hintIndex != type(uint256).max) {

1083 if (pv.votes == type(uint96).max) {   

187  uint96 private constant VETO_VALUE = type(uint96).max;

302  if (govOpts.hosts.length > type(uint8).max) {     
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L359


```solidity
File: contracts/proposals/ProposalExecutionEngine.sol
185  stor.nextProgressDataHash = bytes32(type(uint256).max);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L185


## [G-14] Not using the named return variables when a function returns, wastes deployment gas
When a function returns multiple values without named return variables, it creates a temporary variable to hold the returned values, which can increase the deployment gas cost


There are 12 instances of this issue:

```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
175   function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L175


```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
167   ) public payable onlyDelegateCall returns (uint96 votingPower) {

189   ) public payable onlyDelegateCall returns (uint96 votingPower) {

240   ) external payable onlyDelegateCall returns (uint96 votingPower) {        
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L167


```solidity
File: contracts/party/PartyGovernance.sol
358   ) external view returns (uint96 votingPower) {

372   ) public view returns (uint96 votingPower) {  

492   ) external returns (ITokenDistributor.DistributionInfo memory distInfo) {

595   function accept(uint256 proposalId, uint256 snapIndex) public returns (uint256 totalVotes) {

868   ) private returns (bool completed) {   

908   ) internal view returns (VotingPowerSnapshot memory snap) {

1066  ) private view returns (ProposalStatus status) {                   
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L358



```solidity
File: contracts/proposals/ProposalExecutionEngine.sol
141  function getCurrentInProgressProposalId() external view returns (uint256 id) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L141


## [G-15] Use ERC721A instead ERC721
ERC721A standard, ERC721A is an improvement standard for ERC721 tokens. It was proposed by the Azuki team and used for developing their NFT collection. Compared with ERC721, ERC721A is a more gas-efficient standard to mint a lot of of NFTs simultaneously. It allows developers to mint multiple NFTs at the same gas price. This has been a great improvement due to Ethereum's sky-rocketing gas fee.

[Reffrence](https://nextrope.com/erc721-vs-erc721a-2/)


## [G-16] subtractions can be unchecked to save gas


There are 1 instances of this issue:
```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
238  uint96 refundAmount = newTotalContributions - maxTotalContributions;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L238


## [G-17] Use assembly for math (add, sub, mul, div)
Use assembly for math instead of Solidity. You can check for overflow/underflow in assembly to ensure safety.

Addition:
```
//addition in SolidityCache multiple accesses of a mapping/array
function addTest(uint256 a, uint256 b) public pure {
    uint256 c = a + b;
}
```
Gas: 303
```
//addition in assembly
function addAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := add(a, b)
        if lt(c, a) {
            mstore(0x00, "overflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 263
```
Subtraction
```
//subtraction in Solidity
function subTest(uint256 a, uint256 b) public pure {
  uint256 c = a - b;
}
```
Gas: 300
```
//subtraction in assembly
function subAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := sub(a, b)
        if gt(c, a) {
            mstore(0x00, "underflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 263
```
Multiplication
```
//multiplication in Solidity
function mulTest(uint256 a, uint256 b) public pure {
    uint256 c = a * b;
}
```
Gas: 325
```
//multiplication in assembly
function mulAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := mul(a, b)
        if lt(c, a) {
            mstore(0x00, "overflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 265

Division
```
//division in Solidity
function divTest(uint256 a, uint256 b) public pure {
    uint256 c = a * b;
}
```
Gas: 325

```
//division in assembly
function divAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := div(a, b)
        if gt(c, a) {
            mstore(0x00, "underflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 265


## [G-18] Using a positive conditional flow to save a NOT opcode
Estimated savings: 3 gas

```
if (!cond) {
    // branch False
}
else {
    // branch True
}
```
is less efficient than
```
if (cond) {
    // branch True
}
else {
    // branch False
}
```

[Reffrence](https://code4rena.com/reports/2023-01-opensea#g-03-using-a-positive-conditional-flow-to-save-a-not-opcode)

There are 1 instances of this issue:
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
326         if (!s) {
                if (revertOnFailure) {
                    r.rawRevert();
                }
            } else {
                amounts[i] = abi.decode(r, (uint96));
            }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L326-L332

## [G-19] Do-While loops are cheaper than for loops
If you want to push optimization at the expense of creating slightly unconventional code, Solidity do-while loops are more gas efficient than for loops, even if you add an if-condition check for the case where the loop doesn’t execute at all.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.20;

// times == 10 in both tests
contract Loop1 {
    function loop(uint256 times) public pure {
        for (uint256 i; i < times;) {
            unchecked {
                ++i;
            }
        }
    }
}

contract Loop2 {
    function loop(uint256 times) public pure {
        if (times == 0) {
            return;
        }

        uint256 i;

        do {
            unchecked {
                ++i;
            }
        } while (i < times);
    }
}

```

[Reffrence](https://code4rena.com/reports/2023-05-ajna#g-09-use-do-while-loops-instead-of-for-loops)

There are 1 instances of this issue:
```solidity
File: contracts/party/PartyGovernanceNFT.sol
// @audit this "if (tokenIds.length == 0) revert NothingToBurnError();" condition in line number 350
391  for (uint256 j; j < tokenIds.length; ++j) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L391

## [G-20] decrement should be unchecked to save gas

There are 1 instances of this issue:
```solidity
File: contracts/party/PartyGovernance.sol
468  --numHosts;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L468

## [G-21] Using Fixed Bytes Is Cheaper Than Using String
As a rule of thumb, use bytes for arbitrary-length raw byte data and string for arbitrary-length string (UTF-8) data.
If you can limit the length to a certain number of bytes, always use one of bytes1 to bytes32 because they are much cheaper.

There are 4 instances of this issue:
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
44  string name;

46  string symbol;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L44-L46


```solidity
File: contracts/party/PartyGovernanceNFT.sol
82        string memory name_,
83        string memory symbol_,
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L82-L83


## [G-22] avoid emitting empty data to save gas


```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
335   emit Finalized();

397   emit EmergencyExecuteDisabled();
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L335


```solidity
File: contracts/party/PartyGovernance.sol
857   emit EmergencyExecuteDisabled();
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L857


## [G-23] Massive 15k per tx gas savings - use 1 and 2

Using `true` and `false` will trigger gas-refunds, which after London are 1/5 of what they used to be, meaning using `1` and `2` (keeping the slot non-zero), will cost 5k per change (5k + 5k) vs 20k + 5k, saving you 15k gas per function which uses the modifier.

[Reffrence](https://code4rena.com/reports/2023-01-ondo/#g-03-massive-15k-per-tx-gas-savings---use-1-and-2-for-reentrancy-guard)

```solidity
File: contracts/utils/Implementation.sol
29   modifier onlyInitialize() {
        if (initialized) revert AlreadyInitialized();

        initialized = true;
        emit Initialized();

        _;
    }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol#L29-L36

## [G-24] Use Modifiers Instead of Functions To Save Gas
Example of two contracts with modifiers and internal view function:
```
// SPDX-License-Identifier: MIT
pragma solidity 0.8.9;
contract Inlined {
    function isNotExpired(bool _true) internal view {
        require(_true == true, "Exchange: EXPIRED");
    }
function foo(bool _test) public returns(uint){
            isNotExpired(_test);
            return 1;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity 0.8.9;
contract Modifier {
modifier isNotExpired(bool _true) {
        require(_true == true, "Exchange: EXPIRED");
        _;
    }
function foo(bool _test) public isNotExpired(_test)returns(uint){
        return 1;
    }
}
```
Differences:
```
Deploy Modifier.sol
108727
Deploy Inlined.sol
110473
Modifier.foo
21532
Inlined.foo
21556
```
This with 0.8.9 compiler and optimization enabled. As you can see it's cheaper to deploy with a modifier, and it will save you about 30 gas. But sometimes modifiers increase code size of the contract.

There are 4 instances of this issue:

```solidity
File: contracts/party/PartyGovernance.sol
// @audit use in 458 , 666 lines
216 function _assertHost() internal view {
        if (!isHost[msg.sender]) {
            revert NotAuthorized();
        }
    }
// @audit use in 557 ,715, 772 lines
222  function _assertActiveMember() internal view {   

// @audit use in 493,714 lines
258  function _assertNotGloballyDisabled() internal view {     
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L216


```solidity
File: contracts/party/PartyGovernanceNFT.sol
// @audit use is 171,209,237,248,256,264,487 lines
61   function _assertAuthority() internal view {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L

## [G-25] Use assembly in place of abi.decode to extract calldata values more efficiently
Instead of using abi.decode, we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use.

[Reffrence](https://code4rena.com/reports/2023-08-goodentry#gas-optimizations:~:text=%5BG%2D02%5D%20Use%20assembly%20in%20place%20of%20abi.decode%20to%20extract%20calldata%20values%20more%20efficiently)

There are 5 instances of this issue:
```solidity
File: contracts/party/PartyGovernance.sol
892  nextProgressData = abi.decode(resultData, (bytes));
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L892



```solidity
File: contracts/proposals/ProposalExecutionEngine.sol
133  ProposalEngineOpts memory opts = abi.decode(initializeData, (ProposalEngineOpts));

319  (address expectedImpl, bytes memory initData) = abi.decode(proposalData, (address, bytes));
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L


```solidity
File: contracts/proposals/SetGovernanceParameterProposal.sol
28       SetGovernanceParameterProposalData memory proposalData = abi.decode(
            params.proposalData,
            (SetGovernanceParameterProposalData)
        );
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol#L28


```solidity
File: contracts/proposals/SetSignatureValidatorProposal.sol
31      SetSignatureValidatorProposalData memory data = abi.decode(
            params.proposalData,
            (SetSignatureValidatorProposalData)
        );
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L31

## [G-26] Combine multiple for loop
Whenever possible, it's best to avoid running several for loops one after the other. Most of the time, they can be combined. This avoids certain initialization and counting operations. 

```solidity
File: contracts/party/PartyGovernanceNFT.sol
    function rageQuit(
        uint256[] calldata tokenIds,
        IERC20[] calldata withdrawTokens,
        uint256[] calldata minWithdrawAmounts,
        address receiver
    ) external {
        if (tokenIds.length == 0) revert NothingToBurnError();

        // Check if called by an authority.
        bool isAuthority_ = isAuthority[msg.sender];

        // Check if ragequit is allowed.
        uint40 currentRageQuitTimestamp = rageQuitTimestamp;
        if (!isAuthority_) {
            if (currentRageQuitTimestamp != ENABLE_RAGEQUIT_PERMANENTLY) {
                if (
                    currentRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY ||
                    currentRageQuitTimestamp < block.timestamp
                ) {
                    revert CannotRageQuitError(currentRageQuitTimestamp);
                }
            }
        }

        // Used as a reentrancy guard. Will be updated back after ragequit.
        rageQuitTimestamp = DISABLE_RAGEQUIT_PERMANENTLY;

        // Update last rage quit timestamp.
        lastRageQuitTimestamp = uint40(block.timestamp);

        // Sum up total amount of each token to withdraw.
        uint256[] memory withdrawAmounts = new uint256[](withdrawTokens.length);
        {
            IERC20 prevToken;
            for (uint256 i; i < withdrawTokens.length; ++i) {
                // Check if order of tokens to transfer is valid.
                // Prevent null and duplicate transfers.
                if (prevToken >= withdrawTokens[i]) revert InvalidTokenOrderError();

                prevToken = withdrawTokens[i];

                // Check token's balance.
                uint256 balance = address(withdrawTokens[i]) == ETH_ADDRESS
                    ? address(this).balance
                    : withdrawTokens[i].balanceOf(address(this));

                // Add fair share of tokens from the party to total.
                for (uint256 j; j < tokenIds.length; ++j) {
                    // Must be retrieved before burning the token.
                    withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
                }
            }
        }
        {
            // Burn caller's party cards. This will revert if caller is not the
            // the owner or approved for any of the card they are attempting to
            // burn, not an authority, or if there are duplicate token IDs.
            uint96 totalVotingPowerBurned = _burnAndUpdateVotingPower(tokenIds, !isAuthority_);

            // Update total voting power of party.
            _getSharedProposalStorage().governanceValues.totalVotingPower -= totalVotingPowerBurned;
        }
        {
            uint16 feeBps_ = feeBps;
            for (uint256 i; i < withdrawTokens.length; ++i) {
                IERC20 token = withdrawTokens[i];
                uint256 amount = withdrawAmounts[i];

                // Take fee from amount.
                uint256 fee = (amount * feeBps_) / 1e4;

                if (fee > 0) {
                    amount -= fee;

                    // Transfer fee to fee recipient.
                    if (address(token) == ETH_ADDRESS) {
                        payable(feeRecipient).transferEth(fee);
                    } else {
                        token.compatTransfer(feeRecipient, fee);
                    }
                }

                if (amount > 0) {
                    uint256 minAmount = minWithdrawAmounts[i];

                    // Check amount is at least minimum.
                    if (amount < minAmount) {
                        revert BelowMinWithdrawAmountError(amount, minAmount);
                    }

                    // Transfer token from party to recipient.
                    if (address(token) == ETH_ADDRESS) {
                        payable(receiver).transferEth(amount);
                    } else {
                        token.compatTransfer(receiver, amount);
                    }
                }
            }
        }

        // Update ragequit timestamp back to before.
        rageQuitTimestamp = currentRageQuitTimestamp;

        emit RageQuit(msg.sender, tokenIds, withdrawTokens, receiver);
    }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L344-L448

`fix code:here combine two loop in one`

```solidity
function rageQuit(
    uint256[] calldata tokenIds,
    IERC20[] calldata withdrawTokens,
    uint256[] calldata minWithdrawAmounts,
    address receiver
) external {
    if (tokenIds.length == 0) revert NothingToBurnError();

    // Check if called by an authority.
    bool isAuthority_ = isAuthority[msg.sender];

    // Check if ragequit is allowed.
    uint40 currentRageQuitTimestamp = rageQuitTimestamp;
    if (!isAuthority_) {
        if (currentRageQuitTimestamp != ENABLE_RAGEQUIT_PERMANENTLY) {
            if (currentRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY || currentRageQuitTimestamp < block.timestamp) {
                revert CannotRageQuitError(currentRageQuitTimestamp);
            }
        }
    }

    // Used as a reentrancy guard. Will be updated back after ragequit.
    rageQuitTimestamp = DISABLE_RAGEQUIT_PERMANENTLY;

    // Update last rage quit timestamp.
    lastRageQuitTimestamp = uint40(block.timestamp);

    // Sum up total amount of each token to withdraw.
    uint256[] memory withdrawAmounts = new uint256[](withdrawTokens.length);
    IERC20 prevToken;
    IERC20 token;
    uint256 balance;
    for (uint256 i = 0; i < withdrawTokens.length; ++i) {
        token = withdrawTokens[i];

        // Check if order of tokens to transfer is valid.
        // Prevent null and duplicate transfers.
        if (prevToken >= token) revert InvalidTokenOrderError();

        prevToken = token;

        // Check token's balance.
        balance = address(token) == ETH_ADDRESS ? address(this).balance : token.balanceOf(address(this));

        // Add fair share of tokens from the party to total.
        for (uint256 j = 0; j < tokenIds.length; ++j) {
            // Must be retrieved before burning the token.
            withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
        }

        // Burn caller's party cards. This will revert if caller is not the
        // owner or approved for any of the card they are attempting to
        // burn, not an authority, or if there are duplicate token IDs.
        uint96 totalVotingPowerBurned = _burnAndUpdateVotingPower(tokenIds, !isAuthority_);

        // Update total voting power of party.
        _getSharedProposalStorage().governanceValues.totalVotingPower -= totalVotingPowerBurned;

        uint16 feeBps_ = feeBps;
        uint256 amount;
        uint256 fee;
        uint256 minAmount;
        if (withdrawAmounts[i] > 0) {
            amount = withdrawAmounts[i];

            // Take fee from amount.
            fee = (amount * feeBps_) / 1e4;

            if (fee > 0) {
                amount -= fee;

                // Transfer fee to fee recipient.
                if (address(token) == ETH_ADDRESS) {
                    payable(feeRecipient).transferEth(fee);
                } else {
                    token.compatTransfer(feeRecipient, fee);
                }
            }

            minAmount = minWithdrawAmounts[i];

            // Check amount is at least minimum.
            if (amount < minAmount) {
                revert BelowMinWithdrawAmountError(amount, minAmount);
            }

            // Transfer token from party to recipient.
            if (address(token) == ETH_ADDRESS) {
                payable(receiver).transferEth(amount);
            } else {
                token.compatTransfer(receiver, amount);
            }
        }
    }

    // Update ragequit timestamp back to before.
    rageQuitTimestamp = currentRageQuitTimestamp;

    emit RageQuit(msg.sender, tokenIds, withdrawTokens, receiver);
}
```

## [G-27] Ternary operation is cheaper than if-else statement

```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
186         if (totalContributions >= minTotalContributions) {
                return CrowdfundLifecycle.Won;
            } else {
                return CrowdfundLifecycle.Lost;
            }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L186-L190


```solidity
File: contracts/party/PartyGovernance.sol
435         if (snaps[mid].timestamp > timestamp) {
                // Entry is too recent.
                high = mid;
            } else {
                // Entry is older. This is our best guess for now.
                low = mid + 1;
            }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L435-L441



```solidity
File: contracts/party/PartyGovernanceNFT.sol
419                 if (address(token) == ETH_ADDRESS) {
                        payable(feeRecipient).transferEth(fee);
                    } else {
                        token.compatTransfer(feeRecipient, fee);
                    }

435                 if (address(token) == ETH_ADDRESS) {
                        payable(receiver).transferEth(amount);
                    } else {
                        token.compatTransfer(receiver, amount);
                    }                    
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L419-L423


## [G‑28] Unlimited gas consumption risk due to external call recipients
When calling an external function without specifying a gas limit , the called contract may consume all the remaining gas, causing the tx to be reverted. To mitigate this, it is recommended to explicitly set a gas limit when making low level external calls.

There are 2 instances of this issue:
```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
379   (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L379


```solidity
File: contracts/party/PartyGovernance.sol
846  (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L846


## [G‑29] Using assembly to revert with an error message
When reverting in solidity code, it is common practice to use a require or revert statement to revert execution with an error message. This can in most cases be further optimized by using assembly to revert with the error message.


Here’s an example;

```solidity
/// calling restrictedAction(2) with a non-owner address: 24042
contract SolidityRevert {
    address owner;
    uint256 specialNumber = 1;

    constructor() {
        owner = msg.sender;
    }

    function restrictedAction(uint256 num)  external {
        require(owner == msg.sender, "caller is not owner");
        specialNumber = num;
    }
}

/// calling restrictedAction(2) with a non-owner address: 23734
contract AssemblyRevert {
    address owner;
    uint256 specialNumber = 1;

    constructor() {
        owner = msg.sender;
    }

    function restrictedAction(uint256 num)  external {
        assembly {
            if sub(caller(), sload(owner.slot)) {
                mstore(0x00, 0x20) // store offset to where length of revert message is stored
                mstore(0x20, 0x13) // store length (19)
                mstore(0x40, 0x63616c6c6572206973206e6f74206f776e657200000000000000000000000000) // store hex representation of message
                revert(0x00, 0x60) // revert with data
            }
        }
        specialNumber = num;
    }
}
```
From the example above we can see that we get a gas saving of over 300 gas when reverting wth the same error message with assembly against doing so in solidity. This gas savings come from the memory expansion costs and extra type checks the solidity compiler does under the hood.


## [G‑30] Split revert statements
Similar to splitting require statements, you will usually save some gas by not having a boolean operator in the if statement.

```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
348 if (fundingSplitRecipient_ == address(0) || fundingSplitBps_ == 0) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L348

```solidity
File: contracts/party/PartyGovernance.sol
979  if (newDelegate == address(0) || oldDelegate == address(0)) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L797


## [G-31] Write gas-optimal for-loops
Note: As of [Solidity 0.8.22](https://soliditylang.org/blog/2023/10/25/solidity-0.8.22-release-announcement/), this trick is done automatically by the compiler and does not need to be done explicitly.

This is what a gas-optimal for loop looks like, if you combine the two tricks above:

```
for (uint256 i; i < limit; ) {
    
    // inside the loop
    
    unchecked {
        ++i;
    }
}
```
The two differences here from a conventional for loop is that i++ becomes ++i (as noted above), and it is unchecked because the limit variable ensures it won’t overflow.


## [G‑32] Avoid updating storage when the value hasn't changed
Manipulating storage in solidity is gas-intensive. It can be optimized by avoiding unnecessary storage updates when the new value equals the existing value. If the old value is equal to the new value, not re-storing the value will avoid a Gsreset (2900 gas), potentially at the expense of a Gcoldsload (2100 gas) or a Gwarmaccess (100 gas).

### Note : this instances is missed from bots
There are 7 instances:
```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
145   minContribution = opts.minContribution;
146   maxContribution = opts.maxContribution;

151   minTotalContributions = opts.minTotalContributions;

159   maxTotalContributions = opts.maxTotalContributions;

168   fundingSplitBps = opts.fundingSplitBps;
169   fundingSplitRecipient = opts.fundingSplitRecipient;

244   totalContributions = newTotalContributions;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L145