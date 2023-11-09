# Party Protocol Smart Contracts Analysis

## Index
- [Party Protocol Smart Contracts Analysis](#party-protocol-smart-contracts-analysis)
  - [Index](#index)
  - [1. Party Protocol: A Comprehensive Overview](#1-party-protocol-a-comprehensive-overview)
    - [The protocol has two primary phases:](#the-protocol-has-two-primary-phases)
  - [2. Main Contracts and Their Analysis Through Diagrams](#2-main-contracts-and-their-analysis-through-diagrams)
    - [Party Protocol Scope:](#party-protocol-scope)
      - [InitialETHCrowdfund.sol:](#initialethcrowdfundsol)
      - [ETHCrowdfundBase.sol:](#ethcrowdfundbasesol)
      - [PartyGovernance.sol:](#partygovernancesol)
      - [PartyGovernanceNFT.sol:](#partygovernancenftsol)
      - [ProposalExecutionEngine.sol:](#proposalexecutionenginesol)
      - [ProposalStorage.sol:](#proposalstoragesol)
      - [SetGovernanceParameterProposal.sol:](#setgovernanceparameterproposalsol)
      - [SetSignatureValidatorProposal.sol:](#setsignaturevalidatorproposalsol)
      - [OffChainSignatureValidator.sol:](#offchainsignaturevalidatorsol)
      - [Implementation.sol:](#implementationsol)
  - [3. Approach taken in evaluating the codebase](#3-approach-taken-in-evaluating-the-codebase)
  - [4. Party Protocol: Architecture](#4-party-protocol-architecture)
  - [5. Systemic and Centralization Risks](#5-systemic-and-centralization-risks)
    - [Systematic Risks:](#systematic-risks)
    - [Centralization Risks:](#centralization-risks)
  - [7. Security Approach of the Project](#7-security-approach-of-the-project)
  - [8. Test analysis](#8-test-analysis)
  - [9. Conclusion](#9-conclusion)

## 1. Party Protocol: A Comprehensive Overview
The **Party Protocol** aims to establish a standard for group coordination, providing on-chain functionality for essential group behaviors. 
- These behaviors include:

  1. **Formation**: Bringing a group together and pooling resources.
  2. **Coordination**: Making collective decisions and taking actions.
  3. **Distribution**: Sharing resources among group members.

### The protocol has two primary phases: 
- Crowdfund and Governance.

  1. **Crowdfund**: In this phase, users contribute ETH to become members of a group (Party), granting them voting rights and proportional ownership of the Party.

  2. **Governance**: In the governance phase, Party members can create and vote on proposals to execute actions as a group.

Contributions during the Crowdfund phase are a primary means of creating and joining Parties. Crowdfund contracts enable groups to pool resources in various ways and can be tailored to specific behaviors (e.g., acquiring NFTs, raising ETH for a treasury, etc.).

The Crowdfund phase focuses on forming and financing Parties, while the Governance phase involves decision-making and action execution as a group.
![PartyOverview1](https://github.com/catellaTech/party-protocol/blob/main/PartyOverview1.drawio.png?raw=true)

## 2. Main Contracts and Their Analysis Through Diagrams
### Party Protocol Scope:
#### InitialETHCrowdfund.sol: 
  - The contract it is used to conduct an initial fundraising for creating new **Parties** or **groups**.
    ![InitialETHCrowdfund](https://github.com/catellaTech/party-protocol/blob/main/InitialETHCrowdfund.sol.drawio.png?raw=true)

#### ETHCrowdfundBase.sol: 
  - The base contract for **InitialETHCrowdfund**
    ![PartyETHCrowdfundBase](https://github.com/catellaTech/party-protocol/blob/main/PartyETHCrowdfundBase.sol.drawio.png?raw=true)

#### PartyGovernance.sol: 
  - This contract serves as the foundation for the Party contract within the protocol. It encapsulates the      essential governance logic governing the behavior of parties.
    ![PartyPartyGovernance](https://github.com/catellaTech/party-protocol/blob/main/PartyPartyGovernance.drawio.png?raw=true)

#### PartyGovernanceNFT.sol: 
  - This contract serves as the foundational logic for tokens within the Party protocol, and it is inherited by the Party contract, which is the core component of the protocol.
    ![PartyPartyGovernance](https://github.com/catellaTech/party-protocol/blob/main/PartyGovernanceNFT.drawio.png?raw=true)

#### ProposalExecutionEngine.sol: 
  - This contract is invoked via delegatecall by parties to handle the execution logic associated with proposals. Parties also implement a fallback function that performs a static delegate call to interact with the ProposalExecutionEngine.
    ![ProposalExecutionEngine](https://github.com/catellaTech/party-protocol/blob/main/ProposalExecutionEngine.drawio.png?raw=true)

#### ProposalStorage.sol: 
  - This contract facilitates communication and data management between the PartyGovernance and ProposalExecutionEngine contracts.
    ![ProposalStorage](https://github.com/catellaTech/party-protocol/blob/main/ProposalStorage.drawio.png?raw=true)

#### SetGovernanceParameterProposal.sol: 
  - A new proposal type that allows setting governance parameters for the party.
    ![ProposalStorage](https://github.com/catellaTech/party-protocol/blob/main/SetGovernanceParameterProposal.drawio.png?raw=true)

#### SetSignatureValidatorProposal.sol: 
  -  Abstract contract that handles proposals related to setting or updating signature validators for specific signature hashes.
    ![SetSignatureValidatorProposal](https://github.com/catellaTech/party-protocol/blob/main/SetSignatureValidatorProposal.drawio.png?raw=true)

#### OffChainSignatureValidator.sol: 
  -  This contract provides a mechanism for parties in the Party protocol to validate off-chain signatures. It checks the validity of the signature, ensures the signer is a member of the party with sufficient voting power, and applies a customizable signing threshold for added security.
    ![OffChainSignatureValidator](https://github.com/catellaTech/party-protocol/blob/main/OffChainSignatureValidator.drawio.png?raw=true)

#### Implementation.sol: 
  -  This contract serves as a utility for implementation contracts utilized through a proxy. It offers helper functions designed to facilitate the interaction between implementation contracts and their proxies.
    ![Implementation](https://github.com/catellaTech/party-protocol/blob/main/Implementation.drawio.png?raw=true)

## 3. Approach taken in evaluating the codebase
- High-level overview : I analyzed the overall codebase in one iteration to get a high-level understanding of the code structure and functionality.

- Documentation review : I studied the documentation to understand the purpose of each contract, its functionality, and how it is connected with other contracts.

- Literature review : I read old audits and known findings, as well as the bot races findings.

- Testing setup : I set up my testing environment and ran the tests to ensure that all tests passed. Used yarn and hardhat to test this protocol or foundry as well.

- Detailed analysis : I started with the detailed analysis of the code base, line by line. I took the necessary notes to ask some questions to the sponsors.

## 4. Party Protocol: Architecture
The architecture is designed to enable the decentralized creation and management of groups **(Parties)** through participation in **crowdfunds**. Each type of **crowdfund** has specific logic for its purpose, and the **Party** **contract** acts as the **core** for **governance** and **party** asset management.

![PartyArquitectura](https://github.com/catellaTech/party-protocol/blob/main/PartyArquitectura.drawio.png?raw=true)

- There is always room for improvements and optimizations in the architectural design.

  - **Usability**: Simplify the user experience as much as possible, from participating in **crowdfunds** to managing **governance** and **party** assets.

  - **Monitoring and Analytics:** Implement monitoring and analytics tools to better understand protocol behavior and provide valuable insights for decision-making.
  
  - **User-Friendliness**: The architecture should be as intuitive as possible for end-users. This might involve a more user-friendly user interface or the automation of processes that currently require manual action.
  
  - **Education and Outreach**: Ensuring that users fully understand how the architecture works and how they can get the most out of it is essential. This could include educational resources and a robust documentation base.


## 5. Systemic and Centralization Risks
### Systematic Risks:

- **Inefficient Governance:** Incapacity to make efficient and fair decisions through the governance process could result in stagnation or suboptimal decisions.

- **Limited Interoperability**: Lack of interoperability with other protocols may limit users' options and flexibility.

### Centralization Risks:

- **Centralized Development:** If development and decision-making are centralized in a small group, there is a risk that decisions may not adequately reflect the needs of the community.

- **Token Concentration**: If a small group of participants holds a significant amount of tokens, they could have a disproportionate influence on decision-making and governance.

- **Power Concentration in Gatekeepers**: If gatekeeping functions are centralized, the power to allow or restrict participation could be misused.

## 7. Security Approach of the Project
What the project can add in the understanding of Security;
- By distributing the project to testnets, ensuring that the audits are carried out in onchain audit. 

- Pause Mechanism This is a chaotic situation, which can be thought of as a choice between decentralization and security.

- Add On-Chain Monitoring System; If On-Chain Monitoring systems such as Forta are added to the project, its security will increase.

For example ; This bot tracks any DEFI transactions in which wrapping, unwrapping, swapping, depositing, or withdrawals occur over a threshold amount. If transactions occur with unusually high token amounts, the bot sends out an alert. https://app.forta.network/bot/0x7f9afc392329ed5a473bcf304565adf9c2588ba4bc060f7d215519005b8303e3

- After the Code4rena audit is completed and the project is live, I recommend the audit process to continue, projects like immunefi do this. https://immunefi.com/

## 8. Test analysis
The audit scope of the contracts to be reviewed is 62%, with the aim of reaching 100% to increase the safety. 

## 9. Conclusion 
The **Party Protocol** is designed to streamline group coordination on the blockchain, fostering the creation of groups, facilitating decision-making processes, and ensuring efficient resource distribution. Notably, it refrains from engaging with **ERC20** tokens employing rebase or transfer fees. Instead, it seamlessly integrates with a diverse array of **ERC721** tokens possessing liquidity and listings on platforms such as **OpenSea**.

This protocol is set to launch on both the **Ethereum** **Mainnet** and **Base** **Mainnet**, embodying two pivotal roles of trust: **Hosts**, endowed with the authority to veto proposals and configure **"Rage Quit"** and **Authorities**, possessing root access for managing sensitive operations.

Emphasizing security measures against **denial-of-service attacks**, the protocol anticipates strict adherence to multiple Ethereum Improvement Proposals. This commitment ensures harmonious interoperability with other projects within the Ethereum network, enhancing overall compatibility.

### Time spent:
15 hours