# Introduction
The analysis is based on assessment of ```Party Protocol``` with focus mainly on in-scope contracts i.e.; ```InitialETHCrowdfund```,```ETHCrowdfundBase```, ```PartyGovernance```, ```PartyGovernanceNFT```, ```ProposalExecutionEngine```, ```ProposalStorage```, ```SetGovernanceParameterProposal```, ```SetSignatureValidatorProposal```, ```OffChainSignatureValidator``` and ```Implementation```.

 The ananlysis helps in identifyin contracts context that are mainly focused around the concept of decentralized governance, proposal execution, parameter modification, and signature validation.

 
The ```InitialETHCrowdfund``` initiates crowdfunding, ```ETHCrowdfundBase``` manages the crowdfunding lifecycle.

```PartyGovernance``` and its associated NFT contract handle decentralized governance, where proposals are managed, voted upon, and executed.

```ProposalExecutionEngine``` manages the execution of different proposal types, using shared storage defined in ```ProposalStorage```.

```SetGovernanceParameterProposal``` and ```SetSignatureValidatorProposal``` manage changes to governance parameters and signature validators. ```OffChainSignatureValidator``` verifies off-chain signatures for parties.


# Mechanism Review
The ```InitialETHCrowdfund``` contract in Solidity is used to bootstrap a party with funds. It allows users to contribute Ether (ETH) to the crowdfund, either for themselves or on behalf of others. Contributions can be made in batches and are credited to party cards, which can be minted or existing. The contract also allows for refunds if the crowdfund fails. The contract uses a gatekeeper for eligibility checks and has a function to initialize the crowdfund with various options. It also includes a function to create a party with specific options.

The ```ETHCrowdfundBase``` is used to manage a crowdfunding campaign for a party. It sets minimum and maximum contribution limits, total contribution goals, and exchange rates for converting contributions to voting power. It handles the lifecycle of the crowdfunding campaign, which includes stages like ```active```, ```won```, ```lost``` and ```finalized```. The contract allows for contributions to be made, voting power to be delegated, and for the campaign to be finalized either when it reaches its goal or when it expires. It also includes emergency execution functions for the DAO.

```PartyGovernanceNFT``` contract is a part of a decentralized governance system. It extends the PartyGovernance and ERC721 contracts, implementing the functionality of a non-fungible token (NFT) with voting power. The contract allows for the minting of NFTs with associated voting power, the adjustment of voting power, and the burning of NFTs. It also includes a ```rageQuit``` function, allowing token holders to burn their NFTs and withdraw their share of assets. The contract uses authorities who have special permissions such as adjusting total voting power and adding or removing other authorities.

The ```PartyGovernance``` contract manages proposals, voting, and execution of decisions. Proposals go through various states like Voting, Defeated, Passed, Ready, InProgress, Complete, and Cancelled. The contract also handles voting power, which can be intrinsic or delegated. It includes functions for proposal creation, voting, vetoing, execution, and cancellation. Special roles like hosts and the party DAO multisig have additional permissions. The contract also manages emergency executions and token distributions. It uses a fallback function to forward unknown read-only calls to the proposal execution engine.

The ```ProposalExecutionEngine``` in the Party protocol manages the execution of various types of proposals. It supports different proposal types like listing on Opensea, Zora, fractionalizing, arbitrary calls, and others. The contract uses ```delegate``` calls for upgradability and maintains a storage structure for state management. It ensures only one proposal is in progress at a time and uses OpenZeppelin IERC1271 interface for signature validation.

The ```ProposalStorage``` contract serves as a shared storage bucket for the ```PartyGovernance``` and ```ProposalExecutionEngine``` contracts. It defines several structures for storing proposal and governance data, including vote duration, execution delay, pass threshold, and total voting power. It stores options for proposal execution, such as enabling authority proposals and allowing operators. The contract uses an explicit storage bucket pattern for upgradability and includes functions for initializing the proposal implementation and accessing the shared storage. It uses assembly for low-level storage access, ensuring efficient and direct control over EVM execution.

The ```SetGovernanceParameterProposal``` contract in the Party protocol allows for the modification of governance parameters. It inherits from the ```ProposalStorage``` contract and defines structure for storing proposal data, including vote duration, execution delay, and pass threshold. The contract includes a function to execute a SetGovernanceParameterProposal, which updates the governance parameters if they are valid. It emits events when these parameters are set. The contract ensures that the vote duration is at least ```1``` hour, execution delay does not exceed ```30``` days and  pass threshold does not exceed ```10000``` basis points.

The ```SetSignatureValidatorProposal``` contract in the Party protocol allows for the setting of signature validators for specific hashes. It defines a structure for storing signature validators and a function to execute ```SetSignatureValidatorProposal```, which sets the validator for a given hash. It also provides function to retrieve the signature validator for a specific hash. The contract uses an explicit storage bucket pattern for upgradability and uses assembly for low-level storage access, ensuring efficient and direct control over EVM execution.

The ```OffChainSignatureValidator``` contract in the Party protocol validates off-chain signatures for parties. The contract maintains a mapping of parties to their signing thresholds. It provides a function to validate an off-chain signature, which requires the signature to be a valid EOA signature from a member in the party with sufficient voting power. It provides functionakity to set the signing threshold for a party. The contract emits an event when the signing threshold is updated. It uses assembly for low-level operations, ensuring efficient and direct control over EVM execution.


# Codebase Quality Analysis
The assessment of codebase reveals various shortcoming that directly impacts codebase security, and workflow which must be considered in order to ensure enhanced working and tackling any risk.

The shortfall in the quality of product are analysed as below

- Potential reentrancy vulnerabilities exists in contracts that make external calls and change state. Ensuring that state changes happen before external calls can mitigate this reentrancy issue.
- Some contracts use unchecked operations that impacts the implementation related to protection essential to avoid potential integer overflow/underflow vulnerabilities.
- Contracts with upgradeability features must have stringent access controls on upgrade functions to prevent unauthorized modifications.
- Some contracts are interacting with external contracts must ensure secure and validated interactions to prevent potential failures or unexpected behaviour.
- Usage of magic numbers in the code can make it less readable making use of constants or variables for these values enhances code readability and maintainability.
- The absence of detailed comments and documentation on some function could hinder understanding for developers reviewing or maintaining the code.

# Centralization Risks

Centralization risks have been identified for the codebase that relates to various context of ```access control```, ``` upgradeability and admin controls```, ```absence of decentralized governance``` and ```ownership and permissions```.

Centralised risk related to access control mechanisms are not appropriately implemented for certain functions as they lack adequate restrictions, there's a risk of centralized control. If only a single address or a small set of addresses hold significant authority, it could centralize control within the system.

Centralised risk related to contracts is possessed for upgrade functionalities are controlled by a limited number of entities may pose centralization risks. A small group of individuals holds the power to upgrade or modify critical parts of the system, this could lead to centralized control.

Contracts lack decentralized governance mechanisms might pose centralization risks. Decision-making or key functionalities depend on a single or limited number of parties without broader community involvement; it could centralize decision-making within the system.

Some contracts heavily rely on a single owner or a small set of owners could introduce centralization risks. If ownership or permissions are concentrated within a limited group, it may centralize control over crucial system actions.

## Mitigation Strategy for Centralisation

Mitigation strategies centralization risks following recommendation are made:
- Implementing decentralized governance by introducing mechanisms for broader community participation in decision-making.
- Distributed access control recommendation is made for ensuring access control is distributed among multiple entities, preventing a single point of control.
- Recommendation for reducing dependency on single entities is necessary for fostering a more distributed control structure.
- Transparent and open processes is recommended to establish processes for upgrades, decision-making and permissions to minimize centralization tendencies.

 
# Systemic Risks
Systemic risks or recurring issues that are found and identified throughout the codebase of ```Party Protocol``` are mentioned as below:

**Re-entrancy vulnerability** appears across multiple contracts, posing a systemic risk. Reentrancy vulnerabilities arise from external calls made within functions as even if mitigated in individual instances is not uniformly addressed for all functions and the risk remains across the codebase.

**Access control weakness** in several contracts exists lack of access control mechanisms. This systemic risk can potentially lead to unauthorized access to critical functions, undermining the security and integrity of the entire system.

**Unbounded gas limit** related to functions involving loops through arrays without gas usage consideration pose a systemic risk of hitting the block gas limit. This issue persists across various contracts, potentially making certain functions un-callable.

**Use of low-level calls** or assembly language introduces complexity and potential risk. The risk of introducing bugs or vulnerabilities through this method is systemic if not consistently managed and monitored across all contracts.

**Insufficient revert for failure cases** as several contracts lack event emission for failure cases, making it challenging to monitor and debug the system in case of failures, posing a systemic risk to the system's transparency and debugging capability.

# Architecture Recommendations
Improvement areas are identified for some contracts that have vital importance and role in the ```Party protocol```. The recommendations are made below separately for each identified contract.
- **ETHCrowdfundBase**
  - The recommendation is made for enhancement of refund mechanism by accurately calculating refund amounts and handling potential scenarios where the contract may not have enough eth for refunds.
  - Splitting the fee transfer process from the finalize function to avoid potential contract lock-up if the recipient fails to receive ether which is related to funding split mechanism improvement.
  - Flash loan protection should be implement by having cool down periods or mechanisms to counter potential flash loan attacks that could compromise voting power control.

- **PartyGovernanceNFT**
  - Reentrancy safeguards should be placed to review the code to ensure that state changes after external calls are managed securely to prevent reentrancy attacks.

  - Access control is needed to double-check the ```isAuthority``` mapping and associated functions to prevent unauthorized access or misuse of authority permissions.

- **ProposalExecutionEngine**
  -  The recommendation is made for access control validation to validate the ```onlyDelegateCall``` modifier thoroughly to ensure proper restrictions on function access.
  - Upgradeability security measures should be placed to reinforce security around the upgrade functionality to restrict access to authorized entities only.
  - Use of Low-Level calls should be handle cautiously to ensure they are secure and not prone to arbitrary behaviour.

- **ProposalStorage**
  - Safe storage pointer handling should be reviewed for inline assembly usage to ensure pointers in storage are handled securely and initialized properly.
- **SetGovernanceParameterProposal**
  - Access control should be strengthen to implement access control mechanisms for the ```_executeSetGovernanceParameter``` function to prevent unauthorized modifications to governance parameters.
  - Avoiding Magic Numbers by defining constants for numerical values to improve code readability and maintenance.

# Time Spent
I spent around 35 hours on reviewing and analysis the ```Party Protocol``` contest which helps in identifying and reporting 02 highs and  05 medium severity issues.

The identified issues needs to be mitigated carefully as they will impact the whole project negatively by directly leading to loss of funds and causing governance related issues which is the main element of decentralisation of governance for the protocol. 

 


### Time spent:
035 hours