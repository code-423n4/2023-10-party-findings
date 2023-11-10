### **1. Approach taken in evaluating the codebase**

- We started by reading the readme and documentation to understand the protocol. We took notes on the key points and main invariants, and we identified any parts that were unclear. We then made a list of questions about these parts. 
- After we gained an idea of how the protocol should work, we used a static analyzer to find any easy-to-detect problems in the contracts. We then compared the report to the bots report and selected what we deemed important issues and removed any false positives.
- Finally, we did a thorough and comprehensive manual code review, focusing on the most important and critical areas. We tried to come up with different ways to exploit the system and looked for common contract vulnerabilities. We then documented all of the concerns we found and compared our notes to previous audits of similar protocols.

### **2. Architecture and Codebase quality analysis**
- The protocol allows users to create and join parties by contributing to a crowdfund. Once the crowdfund is successful, the party is created and members are minted NFTs that represent their voting power. Members can then create proposals and vote on their execution.
- In audit scope, the contracts comprises
   > CrowdFund creation and modification contracts - [InitialETHCrowdfund.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol), [ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol);
    
   > Party setup, modifications and proposal creation contracts - [PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol), [PartyGovernanceNFT.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol);
    
   > Proposal execution,storage and parameter modification contracts - [ProposalExecutionEngine.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol), [ProposalStorage.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol);
    
   > Two new proposal contract types - [SetGovernanceParameterProposal.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol)  & [SetSignatureValidatorProposal.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol);
    
   > Signature validator contract - [OffChainSignatureValidator.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol);
    
   > and a utility contract - [Implementation.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol).

- The protocol also makes use of solmate's ERC721 contract, OZ's IERC2981 interface as well as being designed to follow certain EIP standards.
- The codebase appears to be well-written and well structured. With a few exceptions, the function sizes were compact and easy to understand. 
- The test coverage is around 60% which not bad, but can be improved. Consider introducing invariant tests for the larger codebases. 
- Error handling is very good. Custom errors were mostly used instead of repetitive strings which helps to save a lot of gas. The `assert` error was used in one or two cases, this should also be removed to save gas.
- The ProposalExecutionEngine contract uses `tx.origin` which is a currently disputed authorization opcode. This should be kept in mind for future updates incase the it becomes disabled.  

### **3. Protocol risks**
- Centralization as the protocol relies on hosts, authorities, and the partyDAO to perform privileged functions. These entities must be trusted not to abuse their power. Additionally, as authorities are implemented as smart contracts, any vulnerabilities in them could be exploited;
- Malicious party members may try to influence party proposals, by gathering excess voting power to push a proposal that they support or cancelling proposals they're not interested in while it's in progress;
- Bugs in any of the smart contracts could also compromise the protocol;
- Any vulnerabilities in the imported OZ and solady contracts also pose a risk to the protocol's security.

### **4. Conclusion**
- The Party protocol provides an open protocol for group coordination, formation and distribution with the goal of making Ethereum multiplayer. The codebase is solid, well commented and documented, so devs and users will find it quite to easy understand. We recommend taking into consideration discovered issues and mitigating them, implementing constant upgrades and audits to keep the protocol always secure.



### Time spent:
24 hours