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
`contracts/party/PartyGovernanceNFT.sol#L14-L501`
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
## [I-01] SIGNATURE MALLEABILITY
**Impact**
The function ecrecover allows you to convert a valid signature into a different valid signature without requiring knowledge of the private key. It is usually not a problem unless you use signatures to identify items or require them to be uniquely recognisable.
Therefore, depending on the function of the code, this may lead to discrepancies and faulty logic.
**Remediation**
It is recommended to use OpenZeppelin’s ECDSA library that has a wrapper around ecrecover that mitigates this issue. 
The data signer can be recovered using ECDSA.recover, and its address can be compared to verify the signature.
**Location**
```txt
contracts/signature-validators/OffChainSignatureValidator.sol#L58-L58
```
## [I-02] HARD-CODED ADDRESS DETECTED
`contracts/party/PartyGovernanceNFT.sol#L38-L38`
**Impact**
The contract contains an unknown hard-coded address. 
This address might be used for some malicious activity. 
Please check the hard-coded address and its usage.
These hard-coded addresses may be used everywhere throughout the code to define states and interact with the functions and external calls.
Therefore, it is extremely crucial to ensure the correctness of these token contracts as they define various important aspects of the protocol operation.
A misconfigured address mapping could lead to the potential loss of user funds or compromise of the contract owner depending on the function logic.
The following hard-coded addresses were found - 0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE
**Remediation**
It is required to check the address. Also, it is required to check the code of the called contract for vulnerabilities.
Ensure that the contract validates if there's an address or a code change or test cases to validate if the address is correct.
## [I-03] BLOCK VALUES AS A PROXY FOR TIME
**Impact**
Contracts often need access to time values to perform certain types of functionality. 
Values such as block.timestamp and block.number can be used to determine the current time or the time delta. 
However, they are not recommended for most use cases.
For block.number, as Ethereum block times are generally around 14 seconds, the delta between blocks can be predicted. 
The block times, on the other hand, do not remain constant and are subject to change for a number of reasons, e.g., fork reorganisations and the difficulty bomb.
Due to variable block times, block.number should not be relied on for precise calculations of time.
**Remediation**
It is recommended to use trusted external time sources, block numbers instead of timestamps, and/or utilising multiple time sources to increase reliability. 
These practices can help mitigate risks of timestamp manipulation and inaccurate timing, increasing the reliability and security of the smart contract.
**Locations**
```txt
contracts/crowdfund/ETHCrowdfundBase.sol#L163-L163
contracts/crowdfund/ETHCrowdfundBase.sol#L185-L185
contracts/party/PartyGovernanceNFT.sol#L361-L361
contracts/party/PartyGovernanceNFT.sol#L372-L372
contracts/signature-validators/OffChainSignatureValidator.sol#L59-L59
contracts/party/PartyGovernance.sol#L565-L565
contracts/party/PartyGovernance.sol#L622-L622
contracts/party/PartyGovernance.sol#L651-L651
contracts/party/PartyGovernance.sol#L731-L731
contracts/party/PartyGovernance.sol#L734-L734
contracts/party/PartyGovernance.sol#L737-L737
contracts/party/PartyGovernance.sol#L745-L745
contracts/party/PartyGovernance.sol#L811-L811
contracts/party/PartyGovernance.sol#L813-L813
contracts/party/PartyGovernance.sol#L820-L820
contracts/party/PartyGovernance.sol#L954-L954
contracts/party/PartyGovernance.sol#L988-L988
contracts/party/PartyGovernance.sol#L1011-L1011
contracts/party/PartyGovernance.sol#L1086-L1086
```
## [I-04] USE CALL INSTEAD OF TRANSFER OR SEND
**Impact**
The contract was found to be using transfer or send function call. 
This is unsafe as transfer has hard coded gas budget and can fail if the user is a smart contract.
**Remediation**
It is recommended to use call which does not have any hardcoded gas.
**Location**
```txt
contracts/crowdfund/InitialETHCrowdfund.sol#L224-L224
```
## [I-05] MISSING INDEXED KEYWORDS IN EVENTS
**Impact**
Events are essential for tracking off-chain data and when the event paraemters are indexed they can be used as filter options which will help getting only the specific data instead of all the logs.
**Remediation**
Consider adding indexed keyword to crucial event parameters that could be used in off-chain tracking. 
Do remember that the indexed keyword costs more gas.
**Locations**
```txt
contracts/crowdfund/ETHCrowdfundBase.sol#L84-L84
contracts/party/PartyGovernanceNFT.sol#L29-L29
contracts/party/PartyGovernanceNFT.sol#L30-L30
contracts/party/PartyGovernanceNFT.sol#L31-L31
contracts/party/PartyGovernance.sol#L144-L144
contracts/party/PartyGovernance.sol#L145-L145
contracts/party/PartyGovernance.sol#L146-L146
contracts/party/PartyGovernance.sol#L152-L156
contracts/party/PartyGovernance.sol#L158-L158
contracts/proposals/SetGovernanceParameterProposal.sol#L11-L11
contracts/proposals/SetGovernanceParameterProposal.sol#L13-L13
contracts/proposals/SetGovernanceParameterProposal.sol#L15-L15
```
## [I-06] MISSING UNDERSCORE IN NAMING VARIABLES
**Impact**
Solidity style guide suggests using underscores as the prefix for non-external functions and state variables (private or internal) but the contract was not found to be following the same.
**Remediation**
It is recommended to use an underscore for internal and private variables and functions to be in accordance with the Solidity style guide which will also make the code much easier to read.
**Locations**
```txt
contracts/party/PartyGovernanceNFT.sol#L34-L34
contracts/party/PartyGovernanceNFT.sol#L35-L35
contracts/party/PartyGovernanceNFT.sol#L38-L38
contracts/party/PartyGovernance.sol#L186-L186
contracts/party/PartyGovernance.sol#L187-L187
contracts/proposals/ProposalStorage.sol#L39-L39
contracts/proposals/ProposalStorage.sol#L40-L41
```
## [I-07] IN-LINE ASSEMBLY DETECTED
**Impact**
Inline assembly is a way to access the Ethereum Virtual Machine at a low level. 
This bypasses several important safety features and checks of Solidity. 
This should only be used for tasks that need it and if there is confidence in using it.
Multiple vulnerabilities have been detected previously when the assembly is not properly used within the Solidity code; therefore, caution should be exercised while using them.
**Remediation**
Avoid using inline assembly instructions if possible because it might introduce certain issues in the code if not dealt with properly because it bypasses several safety features that are already implemented in Solidity.
**Locations**
```txt
contracts/signature-validators/OffChainSignatureValidator.sol#L32-L38
contracts/signature-validators/OffChainSignatureValidator.sol#L41-L45
contracts/party/PartyGovernance.sol#L319-L324
contracts/party/PartyGovernance.sol#L407-L416
contracts/party/PartyGovernance.sol#L1158-L1162
contracts/proposals/ProposalStorage.sol#L61-L63
contracts/proposals/ProposalExecutionEngine.sol#L306-L312
contracts/proposals/ProposalExecutionEngine.sol#L337-L339
```
## [L-08] Modifier side effects
## Impact
Solidity functions should always use the Checks-Effects-Interactions pattern which states that the initial stage will contain only checks and validations which resides in the modifiers.
Due to this reason, modifiers should only implement checks and validations inside of it and should not make state changes and external calls.
## Proof of Concept
The contract PartyGovernance was found to be violating this pattern and the modifier onlyPartyDaoOrHost was making sensitive state changes and modifications.
**Vulnerable line of code**
```sol
// Ln 243
        address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
```
**Vulnerable onlyPartyDaoOrHost modifier**
```sol
// contracts/party/PartyGovernance.sol#L242-L248
    modifier onlyPartyDaoOrHost() {
        address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
        if (msg.sender != partyDao && !isHost[msg.sender]) {
            revert NotAuthorized();
        }
        _;
    }
```
## Tools Used
VS Code.
## Recommended Mitigation Steps
Only use modifiers for implementing checks and validations. 
Do not make external calls or state changing actions inside modifiers.
## [L-09] Access control issue
## Impact
Anyone can call the function named setSigningThresholdBps within the OffChainSignatureValidator contract.
**Vulnerable setSigningThresholdBps function to access control**
```sol
    function setSigningThresholdBps(uint96 thresholdBps) external {
        Party party = Party(payable(msg.sender));
        emit SigningThresholdBpsSet(party, signingThersholdBps[party], thresholdBps);
        signingThersholdBps[party] = thresholdBps;
    }
```
## Proof of Concept
Here is a proof of concept (POC) that demonstrates the lack of access controls on the setSigningThresholdBps function. 
This POC assumes that you have a deployed instance of the OffChainSignatureValidator contract.
```sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "./OffChainSignatureValidator.sol";

contract POC {
    OffChainSignatureValidator public offChainSignatureValidator;

    constructor(OffChainSignatureValidator _offChainSignatureValidator) {
        offChainSignatureValidator = OffChainSignatureValidator(_offChainSignatureValidator);
    }

    function exploit() public {
        // The attacker can set the signing threshold BPS to any value
        uint96 newThresholdBps = 5000; // 50%

        offChainSignatureValidator.setSigningThresholdBps(newThresholdBps);

        // Log the new threshold BPS
        emit ThresholdBpsSet(newThresholdBps);
    }

    event ThresholdBpsSet(uint96 newThresholdBps);
}
```
This contract, when deployed and its exploit function called, will set the signing threshold BPS to 50% on the OffChainSignatureValidator contract. 
Since there are no access controls on the setSigningThresholdBps function, this operation will succeed even though the attacker should not have the authority to perform this action.
## Tools Used
VS Code.
## Recommended Mitigation Steps
Add access control modifier to the setSigningThresholdBps function.