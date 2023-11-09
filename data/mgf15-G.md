|ID|Title|Category|Severity|Instances|
|-|:-|:-:|:-:|:-:|
| [[G-1](#g-1--using-bools-for-storage-incurs-overhead)] | Using bools for storage incurs overhead | Gas Optimization | Informational | 15 |
| [[G-2](#g-2--use-assembly-to-check-for-address0)] | Use assembly to check for `address(0)` | Gas Optimization | Informational | 15 |
| [[G-3](#g-3--cache-array-length-outside-of-loop)] | Cache array length outside of loop | Gas Optimization | Informational | 6 |
| [[G-4](#g-4--functions-guaranteed-to-revert-when-called-by-normal-users-can-be-marked-payable)] | Functions guaranteed to revert when called by normal users can be marked payable | Gas Optimization | Informational | 3 |
| [[G-5](#g-5--abiencode-is-less-efficient-than-abiencodepacked)] | `abi.encode()` is less efficient than `abi.encodepacked()` | Gas Optimization | Informational | 3 |
| [[G-6](#g-6-->=-costs-less-gas-than->)] | `>=` costs less gas than `>` | Gas Optimization | Informational | 43 |
| [[G-7](#g-7--x-+=-yx--=-y-costs-more-gas-than-x-=-x-+-yx-=-x---y-for-state-variables)] | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables | Gas Optimization | Informational | 15 |
| [[G-8](#g-8--usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead)] | Usage of `uints/ints` smaller than 32 bytes (256 bits) incurs overhead | Gas Optimization | Informational | 61 |
| [[G-9](#g-9--use-nested-if-and,-avoid-multiple-check-combinations)] | Use nested if and, avoid multiple check combinations | Gas Optimization | Informational | 8 |
| [[G-10](#g-10--don't-initialize-variables-with-default-value)] | Don't initialize variables with default value | Gas Optimization | Informational | 4 |
| [[G-11](#g-11--inverting-the-condition-of-an-if-else-statement-wastes-gas)] | Inverting the condition of an if-else-statement wastes gas | Gas Optimization | Informational | 4 |
| [[G-12](#g-12--gas-saving-is-achieved-by-removing-the-delete-keyword-~60k)] | Gas saving is achieved by removing the `delete` keyword (~60k) | Gas Optimization | Informational | 3 |
| [[G-13](#g-13--use-double-if-statements-instead-of-&&)] | Use double `if` statements instead of `&&` | Gas Optimization | Informational | 8 |
| [[G-14](#g-14--with-assembly,-call-bool-success-transfer-can-be-done-gas-optimized)] | With assembly, `.call (bool success)` transfer can be done gas-optimized | Gas Optimization | Informational | 2 |
| [[G-15](#g-15--use-constants-instead-of-typeuintxmax)] | Use constants instead of `type(uintx).max` | Gas Optimization | Informational | 4 |
| [[G-16](#g-16--use-!=-0-instead-of->-0-for-unsigned-integer-comparison)] | Use `!= 0` instead of `> 0` for unsigned integer comparison | Gas Optimization | Informational | 10 |
| [[G-17](#g-17--use-shift-rightleft-instead-of-divisionmultiplication-if-possible)] | Use shift Right/Left instead of division/multiplication if possible | Gas Optimization | Informational | 2 |
| [[G-18](#g-18--constructors-can-be-marked-payable)] | Constructors can be marked `payable` | Gas Optimization | Informational | 5 |
| [[G-19](#g-19--use-selfbalance-instead-of-addressthisbalance)] | USE `SELFBALANCE()` INSTEAD OF `ADDRESS(THIS).BALANCE` | Gas Optimization | Informational | 1 |
| [[G-20](#g-20--use-bytes32-instead-of-string)] | USE BYTES32 INSTEAD OF STRING | Gas Optimization | Informational | 2 |
| [[G-21](#g-21---multiple-addressid-mappings-can-be-combined-into-a-single-mapping-of-an-addressid-to-a-struct,-where-appropriate)] |  Multiple `address/ID` mappings can be combined into a single mapping of an address/ID to a struct, where appropriate | Gas Optimization | Informational | 10 |
| [[G-22](#g-22--ternary-operation-is-cheaper-than-if-else-statement)] | Ternary operation is cheaper than if-else statement | Gas Optimization | Informational | 14 |
| [[G-23](#g-23--sort-solidity-operations-using-short-circuit-mode)] | Sort Solidity operations using short-circuit mode | Gas Optimization | Informational | 3 |
| [[G-24](#g-24--unnecessary-cast)] | Unnecessary cast | Gas Optimization | Informational | 8 |
| [[G-25](#g-25--calling-length-in-a-for-loop-wastes-gas)] | Calling `.length` in a for loop wastes gas | Gas Optimization | Informational | 6 |
| [[G-26](#g-26--unchecked-{}-can-be-used-on-the-division-of-two-uints-in-order-to-save-gas)] | `unchecked {}` can be used on the division of two `uints` in order to save gas | Gas Optimization | Informational | 11 |
| [[G-27](#g-27--use-do-while-loops-instead-of-for-loops)] | Use do while loops instead of for loops | Gas Optimization | Informational | 6 |
| [[G-28](#g-28--state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage)] | State variables should be cached in stack variables rather than re-reading them from storage | Gas Optimization | Informational | 109 |
| [[G-29](#g-29--state-variables-can-be-packed-into-fewer-storage-slots-by-truncating-timestamp-bytes)] | State variables can be packed into fewer storage slots by truncating timestamp bytes | Gas Optimization | Informational | 1 |

## **G-1** | Using bools for storage incurs overhead

### **Description** :-

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
31:bool disableContributingForExistingCard;
```
#### **Context**:-
[InitialETHCrowdfund.sol#L31](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L31)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
39:bool disableContributingForExistingCard;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L39](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L39)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
101:bool public disableContributingForExistingCard;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L101](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L101)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
104:bool public fundingSplitPaid;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L104](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L104)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
106:bool public emergencyExecuteDisabled;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L106](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L106)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
379:bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
```
#### **Context**:-
[ETHCrowdfundBase.sol#L379](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L379)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
96:bool isDelegated;
```
#### **Context**:-
[PartyGovernance.sol#L96](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L96)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
194:bool public emergencyExecuteDisabled;
```
#### **Context**:-
[PartyGovernance.sol#L194](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L194)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
848:bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
```
#### **Context**:-
[PartyGovernance.sol#L848](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L848)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
353:bool isAuthority_ = isAuthority[msg.sender];
```
#### **Context**:-
[PartyGovernanceNFT.sol#L353](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L353)
```solidity
File:2023-10-party/contracts/proposals/ProposalStorage.sol
```
```solidity
29:bool enableAddAuthorityProposal;
```
#### **Context**:-
[ProposalStorage.sol#L29](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalStorage.sol#L29)
```solidity
File:2023-10-party/contracts/proposals/ProposalStorage.sol
```
```solidity
32:bool allowArbCallsToSpendPartyEth;
```
#### **Context**:-
[ProposalStorage.sol#L32](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalStorage.sol#L32)
```solidity
File:2023-10-party/contracts/proposals/ProposalStorage.sol
```
```solidity
34:bool allowOperators;
```
#### **Context**:-
[ProposalStorage.sol#L34](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalStorage.sol#L34)
```solidity
File:2023-10-party/contracts/proposals/ProposalStorage.sol
```
```solidity
36:bool distributionsRequireVote;
```
#### **Context**:-
[ProposalStorage.sol#L36](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalStorage.sol#L36)
```solidity
File:2023-10-party/contracts/utils/Implementation.sol
```
```solidity
15:bool public initialized;
```
#### **Context**:-
[Implementation.sol#L15](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/utils/Implementation.sol#L15)

## **G-2** | Use assembly to check for `address(0)`

### **Description** :-

Saves 6 gas per instance if using assembly to check for address(0)

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
283:        if (delegate == address(0)
```
#### **Context**:-
[InitialETHCrowdfund.sol#L283](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L283)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
384:        if (address(customMetadataProvider) == address(0)
```
#### **Context**:-
[InitialETHCrowdfund.sol#L384](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L384)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
202:        if (msg.sender == contributor || oldDelegate == address(0)
```
#### **Context**:-
[ETHCrowdfundBase.sol#L202](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L202)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
263:        if (fundingSplitRecipient_ != address(0)
```
#### **Context**:-
[ETHCrowdfundBase.sol#L263](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L263)
```solidity
286:        if (fundingSplitRecipient_ != address(0)
```
#### **Context**:-
[ETHCrowdfundBase.sol#L286](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L286)
```solidity
324:        if (fundingSplitRecipient_ != address(0)
```
#### **Context**:-
[ETHCrowdfundBase.sol#L324](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L324)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
462:        if (newPartyHost != address(0)
```
#### **Context**:-
[PartyGovernance.sol#L462](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L462)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
951:        oldDelegate = oldDelegate == address(0)
```
#### **Context**:-
[PartyGovernance.sol#L951](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L951)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
953:        delegate = delegate == address(0)
```
#### **Context**:-
[PartyGovernance.sol#L953](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L953)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
981:        if (newDelegate == address(0) || oldDelegate == address(0)
```
#### **Context**:-
[PartyGovernance.sol#L981](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L981)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
196:        if (delegate_ != address(0)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L196](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L196)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
128:        if (oldImpl != address(0)
```
#### **Context**:-
[ProposalExecutionEngine.sol#L128](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L128)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
231:        if (address(validator) != address(0)
```
#### **Context**:-
[ProposalExecutionEngine.sol#L231](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L231)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
234:        if (tx.origin == address(0)
```
#### **Context**:-
[ProposalExecutionEngine.sol#L234](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L234)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
236:            if (address(validator) == address(0)
```
#### **Context**:-
[ProposalExecutionEngine.sol#L236](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L236)

## **G-3** | Cache array length outside of loop

### **Description** :-

If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
260:for (uint256 i; i < args.recipients.length; ++i) {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L260](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L260)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
306:for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
```
#### **Context**:-
[PartyGovernance.sol#L306](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L306)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
102:for (uint256 i; i < authorities.length; ++i) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L102](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L102)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
272:for (uint256 i; i < tokenIds.length; ++i) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L272](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L272)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
378:for (uint256 i; i < withdrawTokens.length; ++i) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L378](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L378)
```solidity
408:for (uint256 i; i < withdrawTokens.length; ++i) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L408](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L408)

## **G-4** | Functions guaranteed to revert when called by normal users can be marked payable

### **Description** :-

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
857:function disableEmergencyExecute() external onlyPartyDaoOrHost {
```
#### **Context**:-
[PartyGovernance.sol#L857](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L857)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
479:function addAuthority(address authority) external onlySelf {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L479](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L479)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
207:function cancelProposal(uint256 proposalId) external onlyDelegateCall {
```
#### **Context**:-
[ProposalExecutionEngine.sol#L207](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L207)

## **G-5** | `abi.encode()` is less efficient than `abi.encodepacked()`

### **Description** :-

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
286:abi.encode(proposalEngineOpts)
```
#### **Context**:-
[PartyGovernance.sol#L286](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L286)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
286:abi.encode(
```
#### **Context**:-
[PartyGovernance.sol#L286](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L286)
```solidity
403:abi.encode(
```
#### **Context**:-
[PartyGovernance.sol#L403](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L403)

## **G-6** | `>=` costs less gas than `>`

### **Description** :-

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
141:        if (initialContribution > 0) {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L141](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L141)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
211:        for (uint256 i; i < numContributions; ++i) {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L211](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L211)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
224:        if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);
```
#### **Context**:-
[InitialETHCrowdfund.sol#L224](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L224)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
260:        for (uint256 i; i < args.recipients.length; ++i) {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L260](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L260)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
331:        if (amount > 0) {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L331](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L331)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
357:        for (uint256 i; i < numRefunds; ++i) {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L357](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L357)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
379:        for (uint i = 0; i < authoritiesLength - 1; ++i) {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L379](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
142:        if (opts.minContribution > opts.maxContribution) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L142](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L142)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
148:        if (opts.minTotalContributions > opts.maxTotalContributions) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L148](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L148)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
223:        if (amount > maxContribution_) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L223](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L223)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
239:            if (refundAmount > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L239](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L239)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
256:        if (amount < minContribution_) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L256](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L256)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
263:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L263](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L263)
```solidity
286:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L286](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L286)
```solidity
324:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L324](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L324)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
277:        if (govOpts.feeBps > 1e4) {
```
#### **Context**:-
[PartyGovernance.sol#L277](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L277)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
280:        if (govOpts.passThresholdBps > 1e4) {
```
#### **Context**:-
[PartyGovernance.sol#L280](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L280)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
302:        if (govOpts.hosts.length > type(uint8).max) {
```
#### **Context**:-
[PartyGovernance.sol#L302](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L302)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
306:        for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
```
#### **Context**:-
[PartyGovernance.sol#L306](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L306)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
435:        while (low < high) {
```
#### **Context**:-
[PartyGovernance.sol#L435](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L435)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
437:            if (snaps[mid].timestamp > timestamp) {
```
#### **Context**:-
[PartyGovernance.sol#L437](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L437)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
733:            if (proposal.maxExecutableTime < block.timestamp) {
```
#### **Context**:-
[PartyGovernance.sol#L733](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L733)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
801:                if (cancelDelay > globalMaxCancelDelay) {
```
#### **Context**:-
[PartyGovernance.sol#L801](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L801)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
807:                if (cancelDelay < globalMinCancelDelay) {
```
#### **Context**:-
[PartyGovernance.sol#L807](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L807)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
813:            if (block.timestamp < cancelTime) {
```
#### **Context**:-
[PartyGovernance.sol#L813](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L813)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
822:        proposalState.values.completedTime = uint40(block.timestamp | UINT40_HIGH_BIT);
```
#### **Context**:-
[PartyGovernance.sol#L822](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L822)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
916:                hintIndex < snapsLength &&
```
#### **Context**:-
[PartyGovernance.sol#L916](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L916)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
920:                (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)
```
#### **Context**:-
[PartyGovernance.sol#L920](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L920)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
1058:            flags = flags | LibProposal.PROPOSAL_FLAG_UNANIMOUS;
```
#### **Context**:-
[PartyGovernance.sol#L1058](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1058)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
1061:            flags = flags | LibProposal.PROPOSAL_FLAG_HOSTS_ACCEPT;
```
#### **Context**:-
[PartyGovernance.sol#L1061](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1061)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
1128:        return snapshotNumHosts > 0 && snapshotNumHosts == numHostsAccepted;
```
#### **Context**:-
[PartyGovernance.sol#L1128](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1128)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
102:            for (uint256 i; i < authorities.length; ++i) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L102](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L102)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
179:        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower_) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L179](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L179)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
216:        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L216](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L216)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
272:        for (uint256 i; i < tokenIds.length; ++i) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L272](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L272)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
361:                    currentRageQuitTimestamp < block.timestamp
```
#### **Context**:-
[PartyGovernanceNFT.sol#L361](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L361)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
378:            for (uint256 i; i < withdrawTokens.length; ++i) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L378](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L378)
```solidity
408:            for (uint256 i; i < withdrawTokens.length; ++i) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L408](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L408)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
303:        if (proposalData.length < 4) {
```
#### **Context**:-
[ProposalExecutionEngine.sol#L303](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L303)
```solidity
File:2023-10-party/contracts/proposals/SetGovernanceParameterProposal.sol
```
```solidity
33:            if (proposalData.voteDuration < 1 hours) {
```
#### **Context**:-
[SetGovernanceParameterProposal.sol#L33](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol#L33)
```solidity
File:2023-10-party/contracts/proposals/SetGovernanceParameterProposal.sol
```
```solidity
43:            if (proposalData.executionDelay > 30 days) {
```
#### **Context**:-
[SetGovernanceParameterProposal.sol#L43](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol#L43)
```solidity
File:2023-10-party/contracts/proposals/SetGovernanceParameterProposal.sol
```
```solidity
54:            if (proposalData.passThresholdBps > 10000) {
```
#### **Context**:-
[SetGovernanceParameterProposal.sol#L54](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol#L54)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
73:            (signerVotingPowerBps > totalVotingPower &&
```
#### **Context**:-
[OffChainSignatureValidator.sol#L73](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L73)

## **G-7** | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables

### **Description** :-

Using the addition operator instead of plus-equals saves 113 gas

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
212:            ethAvailable -= args.values[i];
```
#### **Context**:-
[InitialETHCrowdfund.sol#L212](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L212)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
268:            valuesSum += args.values[i];
```
#### **Context**:-
[InitialETHCrowdfund.sol#L268](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L268)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
240:                amount -= refundAmount;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L240](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L240)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
637:        values.votes += votingPower;
```
#### **Context**:-
[PartyGovernance.sol#L637](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L637)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
1010:                newDelegateDelegatedVotingPower -= oldSnap.intrinsicVotingPower;
```
#### **Context**:-
[PartyGovernance.sol#L1010](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1010)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
189:        mintedVotingPower += votingPower_;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L189](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L189)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
223:        mintedVotingPower += votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L223](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L223)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
238:        mintedVotingPower -= votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L238](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L238)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
239:        votingPowerByTokenId[tokenId] -= votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L239](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L239)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
249:        _getSharedProposalStorage().governanceValues.totalVotingPower += votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L249](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L249)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
257:        _getSharedProposalStorage().governanceValues.totalVotingPower -= votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L257](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L257)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
290:            totalVotingPowerBurned += votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L290](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L290)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
304:        mintedVotingPower -= totalVotingPowerBurned;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L304](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L304)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
404:            _getSharedProposalStorage().governanceValues.totalVotingPower -= totalVotingPowerBurned;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L404](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L404)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
416:                    amount -= fee;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L416](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L416)

## **G-8** | Usage of `uints/ints` smaller than 32 bytes (256 bits) incurs overhead

### **Description** :-

When using elements that are smaller than 32 bytes, your contracts gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
29:        uint96 minContribution;
```
#### **Context**:-
[InitialETHCrowdfund.sol#L29](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L29)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
30:        uint96 maxContribution;
```
#### **Context**:-
[InitialETHCrowdfund.sol#L30](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L30)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
32:        uint96 minTotalContributions;
```
#### **Context**:-
[InitialETHCrowdfund.sol#L32](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L32)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
33:        uint96 maxTotalContributions;
```
#### **Context**:-
[InitialETHCrowdfund.sol#L33](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L33)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
34:        uint16 exchangeRateBps;
```
#### **Context**:-
[InitialETHCrowdfund.sol#L34](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L34)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
35:        uint16 fundingSplitBps;
```
#### **Context**:-
[InitialETHCrowdfund.sol#L35](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L35)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
72:        uint96[] values;
```
#### **Context**:-
[InitialETHCrowdfund.sol#L72](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L72)
```solidity
90:        uint96[] values;
```
#### **Context**:-
[InitialETHCrowdfund.sol#L90](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L90)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
37:        uint96 minContribution;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L37](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L37)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
38:        uint96 maxContribution;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L38](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L38)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
40:        uint96 minTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L40](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L40)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
41:        uint96 maxTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L41](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L41)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
42:        uint16 exchangeRateBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L42](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L42)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
43:        uint16 fundingSplitBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L43](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L43)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
63:    error NotEnoughContributionsError(uint96 totalContribution, uint96 minTotalContributions);
```
#### **Context**:-
[ETHCrowdfundBase.sol#L63](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L63)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
64:    error MinGreaterThanMaxError(uint96 min, uint96 max);
```
#### **Context**:-
[ETHCrowdfundBase.sol#L64](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L64)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
65:    error MaxTotalContributionsCannotBeZeroError(uint96 maxTotalContributions);
```
#### **Context**:-
[ETHCrowdfundBase.sol#L65](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L65)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
66:    error BelowMinimumContributionsError(uint96 contributions, uint96 minContributions);
```
#### **Context**:-
[ETHCrowdfundBase.sol#L66](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L66)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
67:    error AboveMaximumContributionsError(uint96 contributions, uint96 maxContributions);
```
#### **Context**:-
[ETHCrowdfundBase.sol#L67](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L67)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
68:    error InvalidExchangeRateError(uint16 exchangeRateBps);
```
#### **Context**:-
[ETHCrowdfundBase.sol#L68](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L68)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
95:    uint96 public minContribution;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L95](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L95)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
98:    uint96 public maxContribution;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L98](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L98)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
109:    uint96 public minTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L109](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L109)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
112:    uint96 public maxTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L112](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L112)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
114:    uint96 public totalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L114](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L114)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
120:    uint16 public exchangeRateBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L120](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L120)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
123:    uint16 public fundingSplitBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L123](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L123)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
199:        uint96 amount
```
#### **Context**:-
[ETHCrowdfundBase.sol#L199](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L199)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
200:    ) internal returns (uint96 votingPower) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L200](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L200)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
222:        uint96 maxContribution_ = maxContribution;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L222](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L222)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
227:        uint96 newTotalContributions = totalContributions + amount;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L227](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L227)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
228:        uint96 maxTotalContributions_ = maxTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L228](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L228)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
238:            uint96 refundAmount = newTotalContributions - maxTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L238](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L238)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
255:        uint96 minContribution_ = minContribution;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L255](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L255)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
262:        uint16 fundingSplitBps_ = fundingSplitBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L262](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L262)
```solidity
285:        uint16 fundingSplitBps_ = fundingSplitBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L285](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L285)
```solidity
323:        uint16 fundingSplitBps_ = fundingSplitBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L323](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L323)
```solidity
347:        uint16 fundingSplitBps_ = fundingSplitBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L347](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L347)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
33:    using LibSafeCast for uint96;
```
#### **Context**:-
[PartyGovernance.sol#L33](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L33)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
78:        uint16 passThresholdBps;
```
#### **Context**:-
[PartyGovernance.sol#L78](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L78)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
80:        uint96 totalVotingPower;
```
#### **Context**:-
[PartyGovernance.sol#L80](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L80)
```solidity
127:        uint96 totalVotingPower;
```
#### **Context**:-
[PartyGovernance.sol#L127](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L127)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
16:    using LibSafeCast for uint96;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L16](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L16)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
45:    uint96 public tokenCount;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L45](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L45)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
49:    uint96 public mintedVotingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L49](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L49)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
172:        uint96 mintedVotingPower_ = mintedVotingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L172](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L172)
```solidity
210:        uint96 mintedVotingPower_ = mintedVotingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L210](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L210)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
42:    error UnsupportedProposalTypeError(uint32 proposalType);
```
#### **Context**:-
[ProposalExecutionEngine.sol#L42](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L42)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
290:            revert UnsupportedProposalTypeError(uint32(pt));
```
#### **Context**:-
[ProposalExecutionEngine.sol#L290](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L290)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
314:        require(uint8(proposalType) <= uint8(type(ProposalType).max));
```
#### **Context**:-
[ProposalExecutionEngine.sol#L314](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L314)
```solidity
File:2023-10-party/contracts/proposals/ProposalStorage.sol
```
```solidity
23:        uint16 passThresholdBps;
```
#### **Context**:-
[ProposalStorage.sol#L23](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalStorage.sol#L23)
```solidity
File:2023-10-party/contracts/proposals/ProposalStorage.sol
```
```solidity
24:        uint96 totalVotingPower;
```
#### **Context**:-
[ProposalStorage.sol#L24](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalStorage.sol#L24)
```solidity
File:2023-10-party/contracts/proposals/SetGovernanceParameterProposal.sol
```
```solidity
21:        uint16 passThresholdBps;
```
#### **Context**:-
[SetGovernanceParameterProposal.sol#L21](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol#L21)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
17:        uint96 oldThresholdBps,
```
#### **Context**:-
[OffChainSignatureValidator.sol#L17](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L17)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
18:        uint96 newThresholdBps
```
#### **Context**:-
[OffChainSignatureValidator.sol#L18](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L18)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
22:    mapping(Party party => uint96 thresholdBps) public signingThersholdBps;
```
#### **Context**:-
[OffChainSignatureValidator.sol#L22](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L22)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
29:        uint8 v;
```
#### **Context**:-
[OffChainSignatureValidator.sol#L29](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L29)

```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
67:        uint96 totalVotingPower = party.getGovernanceValues().totalVotingPower;
```
#### **Context**:-
[OffChainSignatureValidator.sol#L67](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L67)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
68:        uint96 thresholdBps = signingThersholdBps[party];
```
#### **Context**:-
[OffChainSignatureValidator.sol#L68](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L68)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
84:    function setSigningThresholdBps(uint96 thresholdBps) external {
```
#### **Context**:-
[OffChainSignatureValidator.sol#L84](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L84)

## **G-9** | Use nested if and, avoid multiple check combinations

### **Description** :-

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
263:if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L263](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L263)
```solidity
286:if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L286](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L286)
```solidity
324:if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L324](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L324)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
225:if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {
```
#### **Context**:-
[PartyGovernance.sol#L225](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L225)
```solidity
507:if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {
```
#### **Context**:-
[PartyGovernance.sol#L507](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L507)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
179:if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower_) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L179](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L179)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
216:if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L216](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L216)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
62:if (signerVotingPowerBps == 0 && party.balanceOf(signer) == 0) {
```
#### **Context**:-
[OffChainSignatureValidator.sol#L62](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L62)

## **G-10** | Don't initialize variables with default value

### **Description** :-

If a variable is not initialized, it is assumed to have the default value. Explicitly initializing a variable with its default value costs unnecessary gas. For more info, see [Mudit Gupta's Blog](https://mudit.blog/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size/) at point `No need to initialize variables with default values` .

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
379:uint i = 0;
```
#### **Context**:-
[InitialETHCrowdfund.sol#L379](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
306:uint256 i = 0;
```
#### **Context**:-
[PartyGovernance.sol#L306](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L306)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
434:uint256 low = 0;
```
#### **Context**:-
[PartyGovernance.sol#L434](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L434)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
1056:uint256 flags = 0;
```
#### **Context**:-
[PartyGovernance.sol#L1056](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1056)

## **G-11** | Inverting the condition of an if-else-statement wastes gas

### **Description** :-

Flipping the `true` and `false` blocks instead saves 3 gas

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
447:== 0 ?
```
#### **Context**:-
[PartyGovernance.sol#L447](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L447)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
951:= oldDelegate == address(0) ?
```
#### **Context**:-
[PartyGovernance.sol#L951](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L951)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
953:= delegate == address(0) ?
```
#### **Context**:-
[PartyGovernance.sol#L953](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L953)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
157:== 0 ?
```
#### **Context**:-
[PartyGovernanceNFT.sol#L157](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L157)

## **G-12** | Gas saving is achieved by removing the `delete` keyword (~60k)

### **Description** :-

30k gas savings were made by removing the `delete` keyword. The reason for using the `delete` keyword here is to reset the struct values (set to default value) in every operation. However, the struct values do not need to be zero each time the function is run. Therefore, the `delete` key word is unnecessary. If it is removed, around 30k gas savings will be achieved.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
319: delete expiry;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L319](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L319)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
293: delete votingPowerByTokenId[tokenId];
```
#### **Context**:-
[PartyGovernanceNFT.sol#L293](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L293)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
488: delete isAuthority[msg.sender];
```
#### **Context**:-
[PartyGovernanceNFT.sol#L488](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L488)

## **G-13** | Use double `if` statements instead of `&&`

### **Description** :-

If the if statement has a logical AND and is not followed by an else statement, it can be replaced with 2 if statements.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
263:if (fundingSplitRecipient_ != address(0) &&
```
#### **Context**:-
[ETHCrowdfundBase.sol#L263](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L263)
```solidity
286:if (fundingSplitRecipient_ != address(0) &&
```
#### **Context**:-
[ETHCrowdfundBase.sol#L286](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L286)
```solidity
324:if (fundingSplitRecipient_ != address(0) &&
```
#### **Context**:-
[ETHCrowdfundBase.sol#L324](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L324)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
225:if (snap.intrinsicVotingPower == 0 &&
```
#### **Context**:-
[PartyGovernance.sol#L225](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L225)
```solidity
507:if (snap.intrinsicVotingPower == 0 &&
```
#### **Context**:-
[PartyGovernance.sol#L507](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L507)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
179:if (totalVotingPower != 0 &&
```
#### **Context**:-
[PartyGovernanceNFT.sol#L179](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L179)
```solidity
216:if (totalVotingPower != 0 &&
```
#### **Context**:-
[PartyGovernanceNFT.sol#L216](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L216)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
62:if (signerVotingPowerBps == 0 &&
```
#### **Context**:-
[OffChainSignatureValidator.sol#L62](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L62)

## **G-14** | With assembly, `.call (bool success)` transfer can be done gas-optimized

### **Description** :-

`return` data `(bool success,)` has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
379:        (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
```
#### **Context**:-
[ETHCrowdfundBase.sol#L379](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L379)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
848:        (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
```
#### **Context**:-
[PartyGovernance.sol#L848](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L848)

## **G-15** | Use constants instead of `type(uintx).max`

### **Description** :-

it uses more gas in the distribution process and also for each transaction than constant usage.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
187:type(uint96).max
```
#### **Context**:-
[PartyGovernance.sol#L187](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L187)
```solidity
1085:type(uint96).max
```
#### **Context**:-
[PartyGovernance.sol#L1085](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1085)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
185:type(uint256).max
```
#### **Context**:-
[ProposalExecutionEngine.sol#L185](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L185)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
314:type(ProposalType).max
```
#### **Context**:-
[ProposalExecutionEngine.sol#L314](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L314)

## **G-16** | Use `!= 0` instead of `> 0` for unsigned integer comparison

### **Description** :-

It is cheaper to use `!= 0` than > `0` for uint256.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
141:        if (initialContribution > 0) {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L141](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L141)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
224:        if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);
```
#### **Context**:-
[InitialETHCrowdfund.sol#L224](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L224)
```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
331:        if (amount > 0) {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L331](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L331)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
239:            if (refundAmount > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L239](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L239)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
263:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L263](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L263)
```solidity
286:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L286](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L286)
```solidity
324:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L324](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L324)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
1128:        return snapshotNumHosts > 0 && snapshotNumHosts == numHostsAccepted;
```
#### **Context**:-
[PartyGovernance.sol#L1128](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1128)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
415:                if (fee > 0) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L415](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L415)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
426:                if (amount > 0) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L426](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L426)

## **G-17** | Use shift Right/Left instead of division/multiplication if possible

### **Description** :-

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left. While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
436:            uint256 mid = (low + high) / 2;
```
#### **Context**:-
[PartyGovernance.sol#L436](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L436)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
74:                signerVotingPowerBps / totalVotingPower >= thresholdBps)
```
#### **Context**:-
[OffChainSignatureValidator.sol#L74](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L74)

## **G-18** | Constructors can be marked `payable`

### **Description** :-

Payable functions cost less gas to execute, since the compiler does not have to add extra checks to ensure that a payment wasn't provided. A constructor can safely be marked as payable, since only the deployer would be able to pass funds, and the project itself would not pass any funds.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
99:constructor(IGlobals globals) ETHCrowdfundBase(globals)
```
#### **Context**:-
[InitialETHCrowdfund.sol#L99](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L99)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
134:constructor(IGlobals globals)
```
#### **Context**:-
[ETHCrowdfundBase.sol#L134](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L134)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
265:constructor(IGlobals globals)
```
#### **Context**:-
[PartyGovernance.sol#L265](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L265)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
76:constructor(IGlobals globals) payable PartyGovernance(globals) ERC721("", "")
```
#### **Context**:-
[PartyGovernanceNFT.sol#L76](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L76)
```solidity
File:2023-10-party/contracts/utils/Implementation.sol
```
```solidity
17:constructor()
```
#### **Context**:-
[Implementation.sol#L17](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/utils/Implementation.sol#L17)

## **G-19** | USE `SELFBALANCE()` INSTEAD OF `ADDRESS(THIS).BALANCE`

### **Description** :-

Use selfbalance() instead of address(this).balance when getting your contract’s balance of ETH to save gas.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
387:address(this).balance
```
#### **Context**:-
[PartyGovernanceNFT.sol#L387](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L387)

## **G-20** | USE BYTES32 INSTEAD OF STRING

### **Description** :-

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
123:(string memory)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L123](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L123)
```solidity
129:(string memory)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L129](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L129)

## **G-21** |  Multiple `address/ID` mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### **Description** :-

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
131:mapping(address => address) public delegationsByContributor;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L131](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L131)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
141:mapping(address => bool) hasVoted;
```
#### **Context**:-
[PartyGovernance.sol#L141](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L141)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
206:mapping(address => bool) public isHost;
```
#### **Context**:-
[PartyGovernance.sol#L206](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L206)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
208:mapping(address => address) public delegationsByVoter;
```
#### **Context**:-
[PartyGovernance.sol#L208](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L208)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
212:mapping(uint256 => ProposalState) private _proposalStateByProposalId;
```
#### **Context**:-
[PartyGovernance.sol#L212](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L212)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
214:mapping(address => VotingPowerSnapshot[]) private _votingPowerSnapshotsByVoter;
```
#### **Context**:-
[PartyGovernance.sol#L214](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L214)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
57:mapping(uint256 => uint256) public votingPowerByTokenId;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L57](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L57)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
59:mapping(address => bool) public isAuthority;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L59](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L59)
```solidity
File:2023-10-party/contracts/proposals/SetSignatureValidatorProposal.sol
```
```solidity
10:mapping(bytes32 => IERC1271) signatureValidators;
```
#### **Context**:-
[SetSignatureValidatorProposal.sol#L10](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L10)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
22:mapping(Party party => uint96 thresholdBps) public signingThersholdBps;
```
#### **Context**:-
[OffChainSignatureValidator.sol#L22](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L22)

## **G-22** | Ternary operation is cheaper than if-else statement

### **Description** :-

There are instances where a ternary operation can be used instead of if-else statement. In these cases, using ternary operation will save modest amounts of gas.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
308: else {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L308](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L308)
```solidity
366: else {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L366](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L366)
```solidity
408: else {
```
#### **Context**:-
[InitialETHCrowdfund.sol#L408](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L408)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
188: else {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L188](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L188)
```solidity
205: else {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L205](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L205)
```solidity
243: else {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L243](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L243)
```solidity
305: else {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L305](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L305)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
440: else {
```
#### **Context**:-
[PartyGovernance.sol#L440](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L440)
```solidity
468: else {
```
#### **Context**:-
[PartyGovernance.sol#L468](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L468)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
421: else {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L421](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L421)
```solidity
437: else {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L437](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L437)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
175: else {
```
#### **Context**:-
[ProposalExecutionEngine.sol#L175](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L175)
```solidity
200: else {
```
#### **Context**:-
[ProposalExecutionEngine.sol#L200](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L200)
```solidity
289: else {
```
#### **Context**:-
[ProposalExecutionEngine.sol#L289](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L289)

## **G-23** | Sort Solidity operations using short-circuit mode

### **Description** :-

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
202:if (msg.sender == contributor || oldDelegate == address(0)
```
#### **Context**:-
[ETHCrowdfundBase.sol#L202](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L202)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
348:if (fundingSplitRecipient_ == address(0) || fundingSplitBps_ == 0)
```
#### **Context**:-
[ETHCrowdfundBase.sol#L348](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L348)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
981:if (newDelegate == address(0) || oldDelegate == address(0)
```
#### **Context**:-
[PartyGovernance.sol#L981](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L981)

## **G-24** | Unnecessary cast

### **Description** :-

performs an unnecessary cast because value is already of type uint256.  achieves the same result without the need for explicit type casting.

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
1136:uint256(voteCount)
```
#### **Context**:-
[PartyGovernance.sol#L1136](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1136)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
1136:uint256(totalVotingPower)
```
#### **Context**:-
[PartyGovernance.sol#L1136](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1136)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
1136:uint256(passThresholdBps)
```
#### **Context**:-
[PartyGovernance.sol#L1136](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1136)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
34:uint256(keccak256("ENABLE_RAGEQUIT_PERMANENTLY")
```
#### **Context**:-
[PartyGovernanceNFT.sol#L34](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L34)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
35:uint256(keccak256("DISABLE_RAGEQUIT_PERMANENTLY")
```
#### **Context**:-
[PartyGovernanceNFT.sol#L35](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L35)
```solidity
File:2023-10-party/contracts/proposals/ProposalExecutionEngine.sol
```
```solidity
104:uint256(keccak256("ProposalExecutionEngine.Storage")
```
#### **Context**:-
[ProposalExecutionEngine.sol#L104](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L104)
```solidity
File:2023-10-party/contracts/proposals/ProposalStorage.sol
```
```solidity
41:uint256(keccak256("ProposalStorage.SharedProposalStorage")
```
#### **Context**:-
[ProposalStorage.sol#L41](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalStorage.sol#L41)
```solidity
File:2023-10-party/contracts/proposals/SetSignatureValidatorProposal.sol
```
```solidity
14:uint256(keccak256("SetSignatureValidatorProposal.Storage")
```
#### **Context**:-
[SetSignatureValidatorProposal.sol#L14](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L14)

## **G-25** | Calling `.length` in a for loop wastes gas

### **Description** :-

Rather than calling .length for an array in a for loop declaration, it is far more gas efficient to cache this length before and use that instead. This will prevent the array length from being called every loop iteration

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
260:for (uint256 i; i < args.recipients.length; ++i)
```
#### **Context**:-
[InitialETHCrowdfund.sol#L260](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L260)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
306:for (uint256 i = 0; i < govOpts.hosts.length; ++i)
```
#### **Context**:-
[PartyGovernance.sol#L306](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L306)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
102:for (uint256 i; i < authorities.length; ++i)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L102](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L102)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
272:for (uint256 i; i < tokenIds.length; ++i)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L272](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L272)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
378:for (uint256 i; i < withdrawTokens.length; ++i)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L378](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L378)
```solidity
408:for (uint256 i; i < withdrawTokens.length; ++i)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L408](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L408)

## **G-26** | `unchecked {}` can be used on the division of two `uints` in order to save gas

### **Description** :-



#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
212:            ethAvailable -= args.values[i];
```
#### **Context**:-
[InitialETHCrowdfund.sol#L212](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L212)
```solidity
File:2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol
```
```solidity
240:                amount -= refundAmount;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L240](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L240)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
436:            uint256 mid = (low + high) / 
```
#### **Context**:-
[PartyGovernance.sol#L436](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L436)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
1010:                newDelegateDelegatedVotingPower -= oldSnap.intrinsicVotingPower;
```
#### **Context**:-
[PartyGovernance.sol#L1010](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1010)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
238:        mintedVotingPower -= votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L238](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L238)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
239:        votingPowerByTokenId[tokenId] -= votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L239](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L239)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
257:        _getSharedProposalStorage().governanceValues.totalVotingPower -= votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L257](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L257)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
304:        mintedVotingPower -= totalVotingPowerBurned;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L304](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L304)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
404:            _getSharedProposalStorage().governanceValues.totalVotingPower -= totalVotingPowerBurned;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L404](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L404)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
416:                    amount -= fee;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L416](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L416)
```solidity
File:2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol
```
```solidity
74:                signerVotingPowerBps / 
```
#### **Context**:-
[OffChainSignatureValidator.sol#L74](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L74)

## **G-27** | Use do while loops instead of for loops

### **Description** :-

A do while loop will cost less gas since the condition is not being checked for the first iteration. 

#### <ins>**Proof Of Concept**</ins>

```solidity
File:2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol
```
```solidity
260:for (uint256 i; i < args.recipients.length; ++i)
```
#### **Context**:-
[InitialETHCrowdfund.sol#L260](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L260)
```solidity
File:2023-10-party/contracts/party/PartyGovernance.sol
```
```solidity
306:for (uint256 i = 0; i < govOpts.hosts.length; ++i)
```
#### **Context**:-
[PartyGovernance.sol#L306](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L306)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
102:for (uint256 i; i < authorities.length; ++i)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L102](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L102)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
272:for (uint256 i; i < tokenIds.length; ++i)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L272](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L272)
```solidity
File:2023-10-party/contracts/party/PartyGovernanceNFT.sol
```
```solidity
378:for (uint256 i; i < withdrawTokens.length; ++i)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L378](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L378)
```solidity
408:for (uint256 i; i < withdrawTokens.length; ++i)
```
#### **Context**:-
[PartyGovernanceNFT.sol#L408](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L408)
## **G-28** | State variables should be cached in stack variables rather than re-reading them from storage
### **Description** :-
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

#### <ins>Proof Of Concept</ins>


```solidity
135:        _GLOBALS = globals;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L135](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L135)

```solidity
392:            _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET) != msg.sender
```
#### **Context**:-
[ETHCrowdfundBase.sol#L392](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L392)

```solidity
161:        party = opts.party;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L161](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L161)

```solidity
145:        minContribution = opts.minContribution;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L145](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L145)

```solidity
255:        uint96 minContribution_ = minContribution;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L255](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L255)

```solidity
146:        maxContribution = opts.maxContribution;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L146](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L146)

```solidity
222:        uint96 maxContribution_ = maxContribution;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L222](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L222)

```solidity
171:        disableContributingForExistingCard = opts.disableContributingForExistingCard;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L171](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L171)

```solidity
352:        fundingSplitPaid = true;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L352](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L352)

```solidity
396:        emergencyExecuteDisabled = true;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L396](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L396)

```solidity
151:        minTotalContributions = opts.minTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L151](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L151)

```solidity
186:            if (totalContributions >= minTotalContributions) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L186](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L186)

```solidity
301:            uint96 minTotalContributions_ = minTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L301](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L301)

```solidity
159:        maxTotalContributions = opts.maxTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L159](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L159)

```solidity
228:        uint96 maxTotalContributions_ = maxTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L228](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L228)

```solidity
229:        if (newTotalContributions >= maxTotalContributions_) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L229](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L229)

```solidity
230:            totalContributions = maxTotalContributions_;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L230](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L230)

```solidity
238:            uint96 refundAmount = newTotalContributions - maxTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L238](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L238)

```solidity
227:        uint96 newTotalContributions = totalContributions + amount;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L227](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L227)

```solidity
244:            totalContributions = newTotalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L244](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L244)

```solidity
292:        uint96 totalContributions_ = totalContributions;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L292](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L292)

```solidity
325:            totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L325](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L325)

```solidity
163:        expiry = uint40(block.timestamp + opts.duration);
```
#### **Context**:-
[ETHCrowdfundBase.sol#L163](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L163)

```solidity
180:        uint256 expiry_ = expiry;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L180](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L180)

```solidity
185:        if (block.timestamp >= expiry_) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L185](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L185)

```solidity
166:        exchangeRateBps = opts.exchangeRateBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L166](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L166)

```solidity
270:        votingPower = (amount * exchangeRateBps) / 1e4;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L270](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L270)

```solidity
281:        amount = (votingPower * 1e4) / exchangeRateBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L281](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L281)

```solidity
329:        uint96 newVotingPower = (totalContributions_ * exchangeRateBps) / 1e4;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L329](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L329)

```solidity
168:        fundingSplitBps = opts.fundingSplitBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L168](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L168)

```solidity
262:        uint16 fundingSplitBps_ = fundingSplitBps;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L262](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L262)

```solidity
263:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L263](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L263)

```solidity
266:            amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L266](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L266)

```solidity
287:            amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
```
#### **Context**:-
[ETHCrowdfundBase.sol#L287](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L287)

```solidity
348:        if (fundingSplitRecipient_ == address(0) || fundingSplitBps_ == 0) {
```
#### **Context**:-
[ETHCrowdfundBase.sol#L348](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L348)

```solidity
355:        splitAmount = (totalContributions * fundingSplitBps_) / 1e4;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L355](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L355)

```solidity
169:        fundingSplitRecipient = opts.fundingSplitRecipient;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L169](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L169)

```solidity
261:        address payable fundingSplitRecipient_ = fundingSplitRecipient;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L261](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L261)

```solidity
201:        address oldDelegate = delegationsByContributor[contributor];
```
#### **Context**:-
[ETHCrowdfundBase.sol#L201](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L201)

```solidity
204:            delegationsByContributor[contributor] = delegate;
```
#### **Context**:-
[ETHCrowdfundBase.sol#L204](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L204)

```solidity
822:        proposalState.values.completedTime = uint40(block.timestamp | UINT40_HIGH_BIT);
```
#### **Context**:-
[PartyGovernance.sol#L822](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L822)

```solidity
1079:            if (pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT) {
```
#### **Context**:-
[PartyGovernance.sol#L1079](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1079)

```solidity
686:        info.values.votes = VETO_VALUE;
```
#### **Context**:-
[PartyGovernance.sol#L686](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L686)

```solidity
233:            address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
```
#### **Context**:-
[PartyGovernance.sol#L233](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L233)

```solidity
243:        address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
```
#### **Context**:-
[PartyGovernance.sol#L243](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L243)

```solidity
266:        _GLOBALS = globals;
```
#### **Context**:-
[PartyGovernance.sol#L266](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L266)

```solidity
793:            uint256 globalMaxCancelDelay = _GLOBALS.getUint256(
```
#### **Context**:-
[PartyGovernance.sol#L793](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L793)

```solidity
796:            uint256 globalMinCancelDelay = _GLOBALS.getUint256(
```
#### **Context**:-
[PartyGovernance.sol#L796](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L796)

```solidity
858:        emergencyExecuteDisabled = true;
```
#### **Context**:-
[PartyGovernance.sol#L858](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L858)

```solidity
297:        feeBps = govOpts.feeBps;
```
#### **Context**:-
[PartyGovernance.sol#L297](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L297)

```solidity
525:        uint16 feeBps_ = feeBps;
```
#### **Context**:-
[PartyGovernance.sol#L525](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L525)

```solidity
298:        feeRecipient = govOpts.feeRecipient;
```
#### **Context**:-
[PartyGovernance.sol#L298](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L298)

```solidity
524:        address payable feeRecipient_ = feeRecipient;
```
#### **Context**:-
[PartyGovernance.sol#L524](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L524)

```solidity
1146:        preciousListHash = _hashPreciousList(preciousTokens, preciousTokenIds);
```
#### **Context**:-
[PartyGovernance.sol#L1146](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1146)

```solidity
1153:        return preciousListHash == _hashPreciousList(preciousTokens, preciousTokenIds);
```
#### **Context**:-
[PartyGovernance.sol#L1153](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1153)

```solidity
307:            isHost[govOpts.hosts[i]] = true;
```
#### **Context**:-
[PartyGovernance.sol#L307](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L307)

```solidity
467:            isHost[newPartyHost] = true;
```
#### **Context**:-
[PartyGovernance.sol#L467](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L467)

```solidity
472:        isHost[msg.sender] = false;
```
#### **Context**:-
[PartyGovernance.sol#L472](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L472)

```solidity
948:        address oldDelegate = delegationsByVoter[voter];
```
#### **Context**:-
[PartyGovernance.sol#L948](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L948)

```solidity
963:        delegationsByVoter[voter] = delegate;
```
#### **Context**:-
[PartyGovernance.sol#L963](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L963)

```solidity
295:        numHosts = uint8(govOpts.hosts.length);
```
#### **Context**:-
[PartyGovernance.sol#L295](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L295)

```solidity
1128:        return snapshotNumHosts > 0 && snapshotNumHosts == numHostsAccepted;
```
#### **Context**:-
[PartyGovernance.sol#L1128](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1128)

```solidity
386:        values = _proposalStateByProposalId[proposalId].values;
```
#### **Context**:-
[PartyGovernance.sol#L386](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L386)

```solidity
599:        ProposalState storage info = _proposalStateByProposalId[proposalId];
```
#### **Context**:-
[PartyGovernance.sol#L599](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L599)

```solidity
719:        ProposalState storage proposalState = _proposalStateByProposalId[proposalId];
```
#### **Context**:-
[PartyGovernance.sol#L719](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L719)

```solidity
429:        VotingPowerSnapshot[] storage snaps = _votingPowerSnapshotsByVoter[voter];
```
#### **Context**:-
[PartyGovernance.sol#L429](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L429)

```solidity
1032:        VotingPowerSnapshot[] storage voterSnaps = _votingPowerSnapshotsByVoter[voter];
```
#### **Context**:-
[PartyGovernance.sol#L1032](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernance.sol#L1032)

```solidity
329:            oldRageQuitTimestamp == ENABLE_RAGEQUIT_PERMANENTLY ||
```
#### **Context**:-
[PartyGovernanceNFT.sol#L329](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L329)

```solidity
358:            if (currentRageQuitTimestamp != ENABLE_RAGEQUIT_PERMANENTLY) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L358](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L358)

```solidity
321:        if (newRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L321](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L321)

```solidity
330:            oldRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY
```
#### **Context**:-
[PartyGovernanceNFT.sol#L330](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L330)

```solidity
360:                    currentRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY ||
```
#### **Context**:-
[PartyGovernanceNFT.sol#L360](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L360)

```solidity
369:        rageQuitTimestamp = DISABLE_RAGEQUIT_PERMANENTLY;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L369](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L369)

```solidity
386:                uint256 balance = address(withdrawTokens[i]) == ETH_ADDRESS
```
#### **Context**:-
[PartyGovernanceNFT.sol#L386](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L386)

```solidity
419:                    if (address(token) == ETH_ADDRESS) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L419](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L419)

```solidity
77:        _GLOBALS = globals;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L77](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L77)

```solidity
172:        uint96 mintedVotingPower_ = mintedVotingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L172](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L172)

```solidity
179:        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower_) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L179](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L179)

```solidity
181:                votingPower_ = totalVotingPower - mintedVotingPower_;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L181](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L181)

```solidity
189:        mintedVotingPower += votingPower_;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L189](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L189)

```solidity
216:        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower) {
```
#### **Context**:-
[PartyGovernanceNFT.sol#L216](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L216)

```solidity
218:                votingPower = totalVotingPower - mintedVotingPower_;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L218](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L218)

```solidity
223:        mintedVotingPower += votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L223](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L223)

```solidity
238:        mintedVotingPower -= votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L238](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L238)

```solidity
304:        mintedVotingPower -= totalVotingPowerBurned;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L304](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L304)

```solidity
100:        rageQuitTimestamp = rageQuitTimestamp_;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L100](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L100)

```solidity
325:        uint40 oldRageQuitTimestamp = rageQuitTimestamp;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L325](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L325)

```solidity
335:        emit RageQuitSet(oldRageQuitTimestamp, rageQuitTimestamp = newRageQuitTimestamp);
```
#### **Context**:-
[PartyGovernanceNFT.sol#L335](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L335)

```solidity
356:        uint40 currentRageQuitTimestamp = rageQuitTimestamp;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L356](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L356)

```solidity
445:        rageQuitTimestamp = currentRageQuitTimestamp;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L445](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L445)

```solidity
147:        return votingPowerByTokenId[tokenId];
```
#### **Context**:-
[PartyGovernanceNFT.sol#L147](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L147)

```solidity
190:        votingPowerByTokenId[tokenId] = votingPower_;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L190](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L190)

```solidity
224:        uint256 newIntrinsicVotingPower = votingPowerByTokenId[tokenId] + votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L224](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L224)

```solidity
225:        votingPowerByTokenId[tokenId] = newIntrinsicVotingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L225](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L225)

```solidity
239:        votingPowerByTokenId[tokenId] -= votingPower;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L239](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L239)

```solidity
288:            uint96 votingPower = votingPowerByTokenId[tokenId].safeCastUint256ToUint96();
```
#### **Context**:-
[PartyGovernanceNFT.sol#L288](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L288)

```solidity
103:                isAuthority[authorities[i]] = true;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L103](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L103)

```solidity
353:        bool isAuthority_ = isAuthority[msg.sender];
```
#### **Context**:-
[PartyGovernanceNFT.sol#L353](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L353)

```solidity
480:        isAuthority[authority] = true;
```
#### **Context**:-
[PartyGovernanceNFT.sol#L480](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/party/PartyGovernanceNFT.sol#L480)

```solidity
117:        _GLOBALS = globals;
```
#### **Context**:-
[ProposalExecutionEngine.sol#L117](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L117)

```solidity
336:        uint256 slot = _STORAGE_SLOT;
```
#### **Context**:-
[ProposalExecutionEngine.sol#L336](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L336)

```solidity
60:        uint256 s = SHARED_STORAGE_SLOT;
```
#### **Context**:-
[ProposalStorage.sol#L60](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalStorage.sol#L60)

```solidity
52:        uint256 slot = _SET_SIGNATURE_VALIDATOR_PROPOSAL_STORAGE_SLOT;
```
#### **Context**:-
[SetSignatureValidatorProposal.sol#L52](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L52)

```solidity
68:        uint96 thresholdBps = signingThersholdBps[party];
```
#### **Context**:-
[OffChainSignatureValidator.sol#L68](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L68)

```solidity
87:        signingThersholdBps[party] = thresholdBps;
```
#### **Context**:-
[OffChainSignatureValidator.sol#L87](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L87)

```solidity
18:        implementation = address(this);
```
#### **Context**:-
[Implementation.sol#L18](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/utils/Implementation.sol#L18)

```solidity
23:        if (address(this) == implementation) {
```
#### **Context**:-
[Implementation.sol#L23](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/utils/Implementation.sol#L23)

```solidity
41:        return implementation;
```
#### **Context**:-
[Implementation.sol#L41](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/utils/Implementation.sol#L41)

```solidity
32:        initialized = true;
```
#### **Context**:-
[Implementation.sol#L32](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/utils/Implementation.sol#L32)

## **G-29** | State variables can be packed into fewer storage slots by truncating timestamp bytes
### **Description** :-
Some state variables can be safely modified, and as result, the contract will use fewer storage slots. Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

#### <ins>Proof Of Concept</ins>


```solidity
39:    uint256 internal constant PROPOSAL_FLAG_UNANIMOUS = 0x1;
```
#### **Context**:-
[ProposalStorage.sol#L39](https://github.com/code-423n4/2023-10-ethena/blob/main/contracts/proposals/ProposalStorage.sol#L39)