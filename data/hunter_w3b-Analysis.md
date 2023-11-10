# Analysis - Party Protocol Contest

![Party Protocol](https://code4rena.com/_next/image?url=https%3A%2F%2Fstorage.googleapis.com%2Fcdn-c4-uploads-v0%2Fuploads%2FXo2RZzzvmHJ.0&w=256&q=75)

## Description overview of The Party Protocol Contest

The Party Protocol is an open protocol for group coordination on the blockchain. It provides on-chain functionality for group formation, coordination, and distribution. Parties are the core units of the protocol, functioning as group Ethereum accounts with members, inventories, and governance capabilities. Members hold membership NFTs with voting power, and proposals define actions for the party to execute. The protocol has two main phases: crowdfund, where users contribute to become party members, and governance, where members create and vote on proposals. Overall, the Party Protocol aims to enable efficient and decentralized group decision-making and resource sharing on the Ethereum network.

**The key contracts of Party protocol for this Audit are**:

Auditing the key contracts of the Party Protocol is essential to ensure the security of the protocol. Focusing on them first will provide a solid foundation for understanding the protocol's operation and how it manages user assets and stability. Here's why it's important to audit these specific contracts:

- **PartyGovernance.sol**: This contract is crucial for the governance logic of parties within the Party Protocol. As governance plays a significant role in decision-making and the functioning of the protocol. I start by reviewing this contract to ensure that governance-related functions are secure.

- **PartyGovernanceNFT.sol**: Since this contract is also related to governance and tokens, this contract handles the token logic for parties, which is an important aspect of the Party Protocol.

- **InitialETHCrowdfund.sol**: InitialETHCrowdfund contract responsible for the crowdfunding process of creating new ETH parties. Crowdfunding involves the management of funds, contributions, and the creation of new parties.

As the audit of these contracts, I checked for potential security vulnerabilities, such as reentrancy, access control issues, and logic flaws. Additionally, thoroughly test the functions and roles defined in these contracts to make sure they behave as expected.

## System Overview

### Scope

- **InitialETHCrowdfund.sol**: The InitialETHCrowdfund contract allows users to raise initial funds for new parties, offering batch contributions and refunds. The contract integrates with a gatekeeper and supports metadata customization for parties. It manages delegation for voting power during governance, enforces contribution limits, and enables efficient batch operations. It plays a pivotal role in bootstrapping parties within the protocol.

- **ETHCrowdfundBase.sol**: This contract foundational template for creating Ethereum-based crowdfunding campaigns. It supports configurable options for campaigns, tracks contributions, and enforces specific lifecycles.

- **PartyGovernance.sol**: PartyGovernance provides decentralized governance functionality, allowing the creation, voting, and execution of proposals. It manages members' voting power and delegate delegation. Token distribution is supported, and emergency actions can be taken in critical situations, with the ability to revoke these actions.

- **PartyGovernanceNFT.sol**: PartyGovernanceNFT contract implements party governance NFTs that confer voting power according to their intrinsic votingPower value. Only authorized accounts can mint, burn and manage voting power of tokens. Token ownership transfers associated voting rights. It supports ragequit functionality to burn tokens in exchange for a pro-rated share of party funds distributed according to voting power. Metadata and royalty functions are delegated to an upgradable renderer contract for extensibility.

- **ProposalExecutionEngine.sol**:This contract is an upgradable proposal execution engine that handles various types of proposals in a decentralized governance . It imports necessary contracts and interfaces, defines a ProposalType enumeration, manages private state with an explicit storage bucket, and provides functions to execute, cancel, and validate proposals.

- **ProposalStorage.sol**: The ProposalStorage contract defines a shared storage structure for storing configuration options and governance values used by both "PartyGovernance" and the "ProposalExecutionEngine." It includes governance-related values, such as vote duration, execution delay, and pass threshold, as well as options for enabling new authorities, allowing ETH spending via arbitrary calls, permitting operators, and controlling distribution voting. This contract facilitates communication and data sharing between the governance and proposal execution components of the system.

- **SetGovernanceParameterProposal.sol**: Used to execute proposals that update various governance parameters within the system. It includes the ability to set parameters like vote duration, execution delay, and pass threshold expressed in basis points (bps). If the provided parameter values are invalid, the contract will revert with an error. When these parameters are successfully updated, events are emitted to reflect the changes.

- **SetSignatureValidatorProposal.sol**: SetSignatureValidatorProposal manages signature validators for ERC1271 signatures. It enables the update of signature validators associated with specific signature hashes via proposals.Users can retrieve the signature validator for a given hash, the contract is designed for governance systems to modify signature validation methods.

- **OffChainSignatureValidator.sol**: The contract validates off-chain signatures for parties. It verifies that the signer is a party member with sufficient voting power, and the message hash matches. Parties can set their signing threshold, and an event is emitted when the threshold changes.

- **Implementation.sol**: This contract is designed to be used in conjunction with delegate calls to enable upgradable contract logic.

### Privileged Roles

Some privileged roles exercise powers over the Controller contract:

- **Party DAO**

```solidity
    modifier onlyPartyDao() {
        {
            address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
            if (msg.sender != partyDao) {
                revert NotAuthorized();
            }
        }
        _;
    }
```

- **Party host**

```solidity
    modifier onlyPartyDaoOrHost() {
        address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
        if (msg.sender != partyDao && !isHost[msg.sender]) {
            revert NotAuthorized();
        }
        _;
    }
```

- **Owner**

```solidity
    modifier onlyOwner() {
        if (msg.sender != OWNER) revert OnlyOwner();
        _;
    }
```

- **Multisig**

```solidity
   modifier onlyMultisig() {
        if (msg.sender != multiSig) {
            revert OnlyMultiSigError();
        }
        _;
    }
```

- **Pending-Multisig**

```solidity
   modifier onlyPendingMultisig() {
        if (msg.sender != pendingMultiSig) {
            revert OnlyPendingMultiSigError();
        }
        _;
    }
```

### Roles

The system defines different roles, including Members, Hosts, and Authorities.

1. Members have voting power within the party, either through holding a membership NFT or receiving delegated votes.

2. Hosts are special roles with veto power over proposals and the ability to configure Rage Quit.

3. Authorities can perform sensitive operations like minting and burning new memberships and are usually trusted contracts or users.

### States

The system has different states, including Governance (while the crowdfund is ongoing), Inactive, Active, Voting, Defeated, Passed, Ready, In Progress, Complete, and Cancelled. These states represent the lifecycle of proposals within the Party Protocol.

## Approach Taken-in Evaluating The Party Protocol

Accordingly, I analyzed and audited the subject in the following steps;

1.  **Core Protocol Contract Overview**:

    I focused on thoroughly understanding the codebase and providing recommendations to improve its functionality.
    The main goal was to take a close look at the important contracts and how they work together in the Party Protocol.
    I started with the contract that is at the core of the system and from which other contracts interact or depend on. In this case, I start with the following contracts, which play crucial roles in the Party protocol system:

    **Main Contracts I Looked At**

                PartyGovernanceNFT.sol
                PartyGovernance.sol
                ProposalExecutionEngine.sol
                ProposalStorage.sol
                InitialETHCrowdfund.sol

    I started my analysis by examining the PartyGovernanceNFT.sol contract. Becuase this is the core NFT contract that implements the party governance tokens. It defines the token standard, voting power attributes, minting/burning, and ragequit functionality.

    Then, I turned our attention to the PartyGovernance.sol. This contract provides the decentralized governance framework including proposal creation/voting, execution handling, and voting power/delegation management. It works closely with PartyGovernanceNFT.

    Then Dive into ProposalExecutionEngine.sol contract, This handles the backend processing and logic for executing different types of governance proposals. It works with PartyGovernance.

    Then audit ProposalStorage.sol this defines the shared storage structure for governance parameters/values used by PartyGovernance and ProposalExecutionEngine.

    InitialETHCrowdfund.sol - This implements the initial fundraising mechanism, integrating key aspects like metadata, contribution limits, and voting power delegation for new parties.

    The key contracts define the core governance (PartyGovernance, ProposalExecutionEngine), tokens (PartyGovernanceNFT), shared storage (ProposalStorage), and initial funding (InitialETHCrowdfund) aspects. The other contracts provide supporting functionality like specific proposal types, off-chain signatures, and upgradability patterns.

2.  **Documentation Review**:

    Then went to Review [This Docs](https://docs.partydao.org/) for a more detailed and technical explanation of Party protocol.

3.  **Manuel Code Review** In this phase, I initially conducted a line-by-line analysis, following that, I engaged in a comparison mode.

    - **Line by Line Analysis**: Pay close attention to the contract's intended functionality and compare it with its actual behavior on a line-by-line basis.

    - **Comparison Mode**: Compare the implementation of each function with established standards or existing implementations, focusing on the function names to identify any deviations.

## Architecture Description and Diagram

Architecture of the key contracts that are part of the Party protocol:
This architecture forms the backbone of the Party Protocol, enabling the creation and governance of parties, crowdfunding campaigns, and various decentralized operations within the protocol.

1. **Crowdfunding Contracts**:

Contracts in the "crowdfund" directory facilitate crowdfunding campaigns. They include various types of crowdfunds like AuctionCrowdfund, BuyCrowdfund, and more.
The core contract, "InitialETHCrowdfund.sol," allows users to raise initial funds for new parties.

2. **Party Contracts**:

"Party.sol" and "PartyFactory.sol" are key contracts that define parties and party creation.
"PartyGovernance.sol" manages decentralized governance for parties, including creating, voting, and executing proposals.
"PartyGovernanceNFT.sol" implements party governance NFTs that confer voting power.

3. **Proposal Contracts**:

The "proposals" directory contains various proposal-related contracts, including "AddAuthorityProposal," "ArbitraryCallsProposal," and others.
"ProposalExecutionEngine.sol" handles the execution of different proposal types in decentralized governance.
"ProposalStorage.sol" provides shared storage for configuration options and governance values.

4. **Gatekeepers**:

Contracts in the "gatekeepers" directory, such as "AllowListGateKeeper" and "TokenGateKeeper," manage access control and gatekeeping functions.

5. **Market Wrappers**:

Market wrapper contracts in the "market-wrapper" directory integrate with external markets such as Koans and Nouns.

6. **Operators**:

Contracts in the "operators" directory handle various operator functionalities, including batch buys and ERC20 swaps.

7. **Signature Validators**:

The "OffChainSignatureValidator.sol" contract is used for validating off-chain signatures associated with parties.

8. **Tokens**:

Contracts in the "tokens" directory define ERC721 and ERC1155 receivers and interfaces for tokens.

9. **Metadata Rendering**:

Contracts in the "renderers" directory deal with metadata rendering and providers for NFTs.

10. **Utility Contracts**:

The "utils" directory contains utility contracts for handling EIP165, safe casting, and other general-purpose functions.

11. **Vendor Contracts**:

Vendor contracts integrate with external systems and markets, like Koans, Nouns, and Solmate.

### Architecture Feedback

1. Shared dependencies like storage are centralized rather than duplicated across contracts.

2. Integrate oracles to provide reliable and tamper-resistant external data for proposals, particularly if proposals require real-world information.

3. Implement an upgradability mechanism, such as a proxy contract, to allow for the protocol's smart contracts to be upgraded without disrupting existing operations.

4. Enhance the vote delegation mechanism to enable users to delegate their votes to multiple recipients if needed.

## Codebase Quality

Overall, I consider the quality of the Party protocol codebase to be excellent. The code appears to be very mature and well-developed. We have noticed the implementation of various standards adhere to appropriately. Details are explained below:

| Codebase Quality Categories              | Comments                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Code Maintainability and Reliability** | The Party Protocol contracts demonstrates good maintainability through modular structure, consistent naming, and efficient use of libraries. It also prioritizes reliability by handling errors, conducting security checks, and using "immutable" variables. However, for enhanced reliability, consider professional security audits and documentation improvements. Regular updates are crucial in the evolving space.                                                                                                                                                                                                                                                        |
| **Code Comments**                        | The Party codebase contains comments that briefly explain the purpose of important code elements, such as data structures, constants, and function modifiers. While these comments offer some clarity, further in-code comments are needed for individual functions and complex logic to enhance code maintainability and comprehension. Although there are some comments, adding more detailed explanations for complex functions, data structures, and key decisions can be helpful to future developers and auditors. Consider following standardized commenting conventions, such as NatSpec.                                                                                |
| **Documentation**                        | The documentation of the Party project is quite comprehensive and detailed, providing a solid overview of how Party protocol is structured and how its various aspects function. However, we have noticed that there is room for additional details, such as diagrams, to gain a deeper understanding of how different contracts interact and the functions they implement. With considerable enthusiasm. We are confident that these diagrams will bring significant value to the protocol as they can be seamlessly integrated into the existing documentation, enriching it and providing a more comprehensive and detailed understanding for users, developers and auditors. |
| **Testing**                              | The audit scope of the contracts to be audited is 62% and it should be aimed to be 100%.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **Code Structure and Formatting**        | The codebase contracts are well-structured and formatted. It inherits from multiple components, adhering for-example The InitialETHCrowdfund contract follows best practices, facilitates fundraising for new parties. It features contribution functions, a refund mechanism for contributors, and interfaces with other contracts for governance and customization. The code follows good practices and has a clear and structured format.                                                                                                                                                                                                                                     |

## Systemic & Centralization Risks

The analysis provided highlights several significant systemic and centralization risks present in the Party protocol. These risks encompass concentration risk in PartyGovernanceNFT, PartyGovernance, ETHCrowdfundBase risk and more, third-party dependency risk, and centralization risks arising from the existence of an “owner” role in specific contracts. However, the documentation lacks clarity on whether this address represents an externally owned account (EOA) or a contract, warranting the need for clarification. Additionally, the absence of fuzzing and invariant tests could also pose risks to the protocol’s security.

Here's an analysis of potential systemic and centralization risks in the contracts:

### Systemic Risks:

1. **No having, fuzzing and invariant tests could open the door to future vulnerabilities**.

2. If the crowdfund fails in the InitialETHCrowdfund contract, there is no clear fallback and funds/assets could be lost.

3. Once funds are contributed in ETHCrowdfundBasecontract.sol, contributors have no way to withdraw them or influence how the funds are used by the party and The same party could be refunded repeatedly from multiple crowdfunding contracts, inflating its total voting power.

4. The increaseVotingPower and decreaseVotingPower functions in PartyGovernanceNFT contract allow certain authorities to manipulate the voting power of NFT holders. Unauthorized or malicious use of these functions could lead to unfair governance outcomes.

5. The ProposalExecutionEngine executes proposals based on their types and data. If malicious proposals are submitted and approved, they could potentially exploit vulnerabilities in the contract.

6. The SetGovernanceParameterProposal does not enforce a quorum requirement for changing governance parameters. A quorum is an essential component of many governance systems, ensuring that a minimum number of participants are involved in the decision-making process. The absence of a quorum requirement can result in proposals being passed or rejected with insufficient participation.

7. The OffChainSignatureValidator contract enforces a specific signature format (EOA signature) and does not support EIP-712 typed signatures. This limitation restricts the flexibility and compatibility of the signature verification process, potentially excluding alternative signature standards that the broader ecosystem might prefer.

### Centralization Risks:

1. The addAuthority function PartyGovernanceNFT contract allows the contract to add new authorities. If this function is misused, it could centralize control further and The contract has a fee mechanism that could be subject to centralization if the fee recipient address or fee rates are not managed fairly.

2. The isValidSignature function ProposalExecutionEngine contract relies on an external signature validator contract. Depending on how this validator is set up, it could introduce centralization risks if it's controlled by a single entity.

**Properly managing these risks and implementing best practices in security and decentralization will contribute to the sustainability and long-term success of the Party protocol.**

## Conclusion

In general, the Party project exhibits an interesting and well-developed architecture we believe the team has done a good job
regarding the code, but the identified risks need to be addressed, and measures should be implemented to protect the protocol from
potential malicious use cases. Additionally, it is recommended to improve the documentation and comments in the code to enhance
understanding and collaboration among developers. It is also highly recommended that the team continues to invest in security
measures such as mitigation reviews, audits, and bug bounty programs to maintain the security and reliability of the project.


### Time spent:
15 hours