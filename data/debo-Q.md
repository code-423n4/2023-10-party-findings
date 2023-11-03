## [L-01] MSG VALUE IN LOOP
**Impact**
Using msg.value inside a loop is an indication of faulty logic depending on the implemented code. 
It can sometimes lead to loss of funds by allowing the same ETH to be used multiple times which can lead to hacks like Opyn.
**Remediation**
It is recommended to go through the code logic to make sure other functions are not dependent on the function using msg.value in a loop.
**Location**
```txt
contracts/crowdfund/InitialETHCrowdfund.sol#L212-L212
```
## [L-02] MISSING EVENTS
**Impact**
Events are inheritable members of contracts. 
When you call them, they cause the arguments to be stored in the transaction’s log — a special data structure in the blockchain.
These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.
The contract ETHCrowdfundBase was found to be missing these events on the function _initialize which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernanceNFT was found to be missing these events on the function _initialize which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernanceNFT was found to be missing these events on the function decreaseVotingPower which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernanceNFT was found to be missing these events on the function increaseTotalVotingPower which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernanceNFT was found to be missing these events on the function decreaseTotalVotingPower which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernanceNFT was found to be missing these events on the function burn which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernanceNFT was found to be missing these events on the function transferFrom which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernanceNFT was found to be missing these events on the function safeTransferFrom which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernanceNFT was found to be missing these events on the function safeTransferFrom which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernance was found to be missing these events on the function _initialize which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernance was found to be missing these events on the function which would make it difficult or impossible to track these transactions off-chain.
The contract PartyGovernance was found to be missing these events on the function _setPreciousList which would make it difficult or impossible to track these transactions off-chain.
The contract InitialETHCrowdfund was found to be missing these events on the function contribute which would make it difficult or impossible to track these transactions off-chain.
The contract InitialETHCrowdfund was found to be missing these events on the function batchContribute which would make it difficult or impossible to track these transactions off-chain.
The contract InitialETHCrowdfund was found to be missing these events on the function contributeFor which would make it difficult or impossible to track these transactions off-chain.
The contract InitialETHCrowdfund was found to be missing these events on the function batchContributeFor which would make it difficult or impossible to track these transactions off-chain.
The contract InitialETHCrowdfund was found to be missing these events on the function _contribute which would make it difficult or impossible to track these transactions off-chain.
The contract InitialETHCrowdfund was found to be missing these events on the function batchRefund which would make it difficult or impossible to track these transactions off-chain.
The contract InitialETHCrowdfund was found to be missing these events on the function _createParty which would make it difficult or impossible to track these transactions off-chain.
The contract ProposalStorage was found to be missing these events on the function _initProposalImpl which would make it difficult or impossible to track these transactions off-chain.
The contract ProposalExecutionEngine was found to be missing these events on the function cancelProposal which would make it difficult or impossible to track these transactions off-chain.
**Remediation**
Consider emitting events for the functions mentioned above. 
It is also recommended to have the addresses indexed.
**Locations**
```txt
contracts/crowdfund/ETHCrowdfundBase.sol#L140-L172
contracts/party/PartyGovernanceNFT.sol#L81-L110
contracts/party/PartyGovernanceNFT.sol#L236-L242
contracts/party/PartyGovernanceNFT.sol#L247-L250
contracts/party/PartyGovernanceNFT.sol#L255-L258
contracts/party/PartyGovernanceNFT.sol#L310-L314
contracts/party/PartyGovernanceNFT.sol#L451-L455
contracts/party/PartyGovernanceNFT.sol#L458-L462
contracts/party/PartyGovernanceNFT.sol#L465-L474
contracts/party/PartyGovernance.sol#L270-L308
contracts/party/PartyGovernance.sol#L312-L327
contracts/party/PartyGovernance.sol#L1137-L1145
contracts/crowdfund/InitialETHCrowdfund.sol#L164-L176
contracts/crowdfund/InitialETHCrowdfund.sol#L204-L225
contracts/crowdfund/InitialETHCrowdfund.sol#L235-L249
contracts/crowdfund/InitialETHCrowdfund.sol#L255-L273
contracts/crowdfund/InitialETHCrowdfund.sol#L275-L311
contracts/crowdfund/InitialETHCrowdfund.sol#L350-L370
contracts/crowdfund/InitialETHCrowdfund.sol#L372-L435
contracts/proposals/ProposalStorage.sol#L43-L53
contracts/proposals/ProposalExecutionEngine.sol#L207-L223
```
## [L-03] FUNCTION RETURNS TYPE AND NO RETURN
**Impact**
This function specifies a returns keyword in the function signature but does not mention what to return anywhere in the function. 
This forces the function to always return a default value that was specified in the signature despite the calculations inside the function.
**Remediation**
f you don’t need the return value of the function, do not specify returns in the function signature
**Locations**
```txt
contracts/signature-validators/OffChainSignatureValidator.sol#L28-L80
contracts/crowdfund/InitialETHCrowdfund.sol#L372-L435
```
## [L-04] COMPILER VERSION TOO RECENT
**Impact**
The compiler version detected in the code is too recent. 
Therefore, it is not time-tested and may be susceptible to multiple bugs and vulnerabilities, both from the usage and security perspectives. 
**Remediation**
It is suggested to use a compiler version that is neither too recent nor too old i.e., Solidity 0.8.18. 
A stable compiler version should be used that is time-tested by the community, which fixed vulnerabilities introduced in older compiler versions.
**Locations**
```txt
The following compiler versions were detected which were too recent - 
/contracts/crowdfund/ETHCrowdfundBase.sol - 0.8.20
/contracts/party/PartyGovernanceNFT.sol - 0.8.20
/contracts/signature-validators/OffChainSignatureValidator.sol - 0.8.20
/contracts/party/PartyGovernance.sol - 0.8.20
/contracts/crowdfund/InitialETHCrowdfund.sol - 0.8.20
/contracts/proposals/SetSignatureValidatorProposal.sol - 0.8.20
/contracts/proposals/ProposalStorage.sol - 0.8.20
/contracts/proposals/SetGovernanceParameterProposal.sol - 0.8.20
/contracts/proposals/ProposalExecutionEngine.sol - 0.8.20
```
## [L-05] EVENT BASED REENTRANCY
**Impact**
In a Re-entrancy attack, a malicious contract calls back into the calling contract before the first invocation of the function is finished. 
This may cause the different invocations of the function to interact in undesirable ways, especially in cases where the function is updating state variables after the external calls.
In the case of event-based Re-entrancy attacks, events are emitted after an external call leading to missing event calls.
**Remediation**
It is recommended to add a [Re-entrancy Guard] to the functions making external calls. 
The functions should use a Checks-Effects-Interactions pattern. 
The external calls should be executed at the end of the function and all the state-changing and event emits must happen before the call.
**Locations**
```txt
contracts/crowdfund/ETHCrowdfundBase.sol#L339-L359
contracts/crowdfund/ETHCrowdfundBase.sol#L366-L384
contracts/party/PartyGovernanceNFT.sol#L344-L448
contracts/party/PartyGovernance.sol#L771-L834
contracts/party/PartyGovernance.sol#L841-L851
```
## [L-06] MISSING INHERITANCE
**Impact**
- contracts/party/PartyGovernanceNFT.sol#L14-L501
The contract probably intends to inherit the interface IERC721Renderer but does not do so by deriving from it. This suggests missing logic or dead code.
The following functions matched the signatures in the interface: tokenURI, contractURI
**Remediation**
It is recommended to go through the contract logic and fix the missing derivations if needed.
## [L-07] REQUIRE WITH EMPTY MESSAGE
**Impact**
A require statement was detected with an empty message. It takes two parameters and the message part is optional. This is shown to the user when and if the require statement evaluates to false. This message gives more information about the statement and why it gave a false response.
**Remediation**
It is recommended to add a descriptive message, no longer than 32 bytes, inside the require statement to give more detail to the user about why the condition failed.
**Locations**
```txt
contracts/proposals/ProposalExecutionEngine.sol#L313-L313
contracts/proposals/ProposalExecutionEngine.sol#L314-L314
```