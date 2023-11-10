
# GAS OPTIMIZATION

|   no   |  issue  |  instance  |
|------|---------|------------|
|[G‑01]|Use constants instead of type(uintx).max|13|
|[G‑02]|Ternary operation is cheaper than if-else statement|4|
|[G‑03]|Use Modifiers Instead of Functions To Save Gas|4|
|[G‑04]|Avoid contract existence checks by using low level calls|2|
|[G‑05]|Can Make The Variable Outside The Loop To Save Gas|5|
|[G‑06]|Make 3 event parameters indexed when possible|21|
|[G‑07]|Expressions for constant values such as a call to keccak256(), should use immutable rather than constant|3|
|[G‑08]|Using Fixed Bytes Is Cheaper Than Using String|1|
|[G‑09]|Use assembly in place of abi.decode to extract calldata values more efficiently|5|
|[G‑10]|Unlimited gas consumption risk due to external call recipients|3|
|[G‑11]|Pre-increment and pre-decrement are cheaper than +1 ,-1 |3|
|[G‑12]|Access mappings directly rather than using accessor functions|1|
|[G‑13]|abi.encode() is less efficient than abi.encodePacked()|1|
|[G‑14]|With assembly, .call (bool success) transfer can be done gas-optimized|3|
|[G‑15]|Delete variables that you don’t need|1|
|[G‑16]|subtractions can be unchecked to save gas|1|
|[G‑17]|Using a positive conditional flow to save a NOT opcode|1|
|[G‑18]|Use do while loops instead of for loops|1|
|[G‑19]|Not using the named return variables when a function returns, wastes deployment gas|13|
|[G‑20]|Use hardcode address instead address(this)|6|





## [G-01] Use constants instead of type(uintx).max

Using constants instead of type(uintx).max can save gas in some cases because constants are precomputed at compile-time and can be reused throughout the code, whereas type(uintx).max requires computation at runtime



```solidity
File: contracts/proposals/ProposalExecutionEngine.sol
185  stor.nextProgressDataHash = bytes32(type(uint256).max);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L185

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





## [G-02] Ternary operation is cheaper than if-else statement

In Solidity, the ternary operator can sometimes be more gas-efficient than using an if-else statement. 


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





## [G-03] Use Modifiers Instead of Functions To Save Gas

In Solidity, using modifiers instead of standalone functions can sometimes help save gas.
Modifiers are special constructs in Solidity that allow you to add additional checks or behaviors to functions. 


```solidity
File: contracts/party/PartyGovernance.sol
216 function _assertHost() internal view {
        if (!isHost[msg.sender]) {
            revert NotAuthorized();
        }
    }

222  function _assertActiveMember() internal view {   


258  function _assertNotGloballyDisabled() internal view {     
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L216
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L222
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L258



```solidity
File: contracts/party/PartyGovernanceNFT.sol
61   function _assertAuthority() internal view {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L


## [G‑04] Avoid contract existence checks by using low level calls

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. 



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


## [G-05] Can Make The Variable Outside The Loop To Save Gas 

When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements.


```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
358   (bool s, bytes memory r) = address(this).call(
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L358


```solidity
File: contracts/party/PartyGovernanceNFT.sol
273            uint256 tokenId = tokenIds[i];
274            address owner = ownerOf(tokenId);

386            uint256 balance = address(withdrawTokens[i]) == ETH_ADDRESS

410                uint256 amount = withdrawAmounts[i];

413                uint256 fee = (amount * feeBps_) / 1e4;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L273
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L274
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L386
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L410
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L413





## [G-06] Make 3 event parameters indexed when possible

It is the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.
When an event is emitted in Solidity, the EVM (Ethereum Virtual Machine) stores the event's data in a log. By default, all event parameters are non-indexed, meaning that they are included in the log's data section. However, you have the option to mark specific event parameters as indexed.


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

In Solidity, the constant keyword is used to declare functions or variables that can be evaluated at compile-time and do not modify the contract's state. However, when it comes to constant expressions involving expensive operations like cryptographic hashing (e.g., keccak256()), using the immutable keyword can provide gas savings.

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



## [G-08] Using Fixed Bytes Is Cheaper Than Using String

If you can limit the length to a certain number of bytes, always use one of bytes1 to bytes32 because they are much cheaper.

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




## [G-09] Use assembly in place of abi.decode to extract calldata values more efficiently

 we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use. and it will be cheaper .

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
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L133

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L319


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





## [G‑10] Unlimited gas consumption risk due to external call recipients

 it is recommended to explicitly set a gas limit when making low level external calls.

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







## [G-11] Pre-increment and pre-decrement are cheaper than +1 ,-1
In Solidity, using pre-increment and pre-decrement operators (++ and --) can be more gas-efficient than using addition (+) and subtraction (-) operations with a value of 1.

```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
377   uint256 authoritiesLength = opts.authorities.length + 1;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L377


```solidity
File:  contracts/party/PartyGovernance.sol
440   low = mid + 1;

445   return high == 0 ? type(uint256).max : high - 1;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L440
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L445




## [G-12] Access mappings directly rather than using accessor functions
In Solidity, accessing mappings directly rather than using accessor functions can help save gas and improve efficiency. Gas is the unit of computational effort required to execute operations on the Ethereum network, and minimizing gas usage is important for optimizing the cost and performance of smart contracts.



```solidity
File: contracts/party/PartyGovernanceNFT.sol
147   return votingPowerByTokenId[tokenId];
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L147



## [G-13] abi.encode() is less efficient than abi.encodePacked()

abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost of reduced safety, as abi.encodePacked() does not perform any type checking or padding of data.


```solidity
File: contracts/party/PartyGovernance.sol
386  abi.encode(proposalEngineOpts)
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L386



## [G-14] With assembly, .call (bool success) transfer can be done gas-optimized

In Solidity, the .call function in assembly can be used to perform a gas-optimized transfer of funds between contracts. Gas optimization is crucial for minimizing the cost and improving the efficiency of smart contract execution on the Ethereum network.
return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.

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





## [G-15] Delete variables that you don’t need


When a variable is no longer needed in your contract, deleting it can free up storage space and reduce the gas cost associated with storage operations. Storage in Ethereum is relatively expensive, so removing unused variables can result in significant gas savings.



```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
319  delete expiry;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L319




## [G-16] subtractions can be unchecked to save gas


```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
238  uint96 refundAmount = newTotalContributions - maxTotalContributions;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L238



## [G-17] Using a positive conditional flow to save a NOT opcode

In Solidity, using a positive conditional flow instead of a negative conditional flow can sometimes help save a NOT opcode, resulting in gas savings.

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

## [G-18] Use do while loops instead of for loops

A do while loop will cost less gas since the condition is not being checked for the first iteration.

```solidity
File: contracts/party/PartyGovernanceNFT.sol
391  for (uint256 j; j < tokenIds.length; ++j) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L391



## [G-19] Not using the named return variables when a function returns, wastes deployment gas


When a function returns a value in Solidity, you have the option to specify named return variables that provide a clear and explicit definition of the returned values. However, if you do not use named return variables, it can lead to unnecessary gas consumption during contract deployment.


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





## [G-20] Use hardcode address instead address(this)

In Solidity, address(this) is used to obtain the address of the current contract. However, using address(this) requires the deployment code to include an extra CODECOPY operation to load the contract's code into memory. This additional operation incurs gas costs during deployment.

it can be more gas-efficient to use a hardcoded address instead of the address(this) expression, especially if you need to use the same address multiple times in your contract.


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
