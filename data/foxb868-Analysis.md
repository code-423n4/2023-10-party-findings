## **Overview**

The Party Protocol enables decentralized group coordination and governance for forming parties, making proposals, distributing assets, and more. The core component is the Party contract which handles membership as ERC721 tokens, voting power, proposals, and asset custody.

## **SCOPE**

| Contract                                         | Purpose                                                                                      | Libraries used                                                        |
|--------------------------------------------------|----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------|
| contracts/crowdfund/InitialETHCrowdfund.sol      | This contract is a crowdfund for creating a new ETH party                                  |                                                                       |
| contracts/crowdfund/ETHCrowdfundBase.sol         | The base contract for InitialETHCrowdfund                                                 |                                                                       |
| contracts/party/PartyGovernance.sol              | This contract is inherited by the Party contract--the core contract in the protocol. This contract has the governance logic for parties |                                                                       |
| contracts/party/PartyGovernanceNFT.sol           | This contract is inherited by the Party contract--the core contract in the protocol. This contract has the token logic for parties     | solmate/ERC721, openzeppelin/contracts/interfaces/IERC2981.sol      |
| contracts/proposals/ProposalExecutionEngine.sol  | This contract is delegate called from parties for execution logic involved with proposal. Parties also have a fallback that does a static delegate call to the ProposalExecutionEngine |                                                                       |
| contracts/proposals/ProposalStorage.sol          | Shared storage storage bucket that is accessed in multiple contracts                      |                                                                       |
| contracts/proposals/SetGovernanceParameterProposal.sol | A new proposal type that allows setting governance parameters for the party             |                                                                       |
| contracts/proposals/SetSignatureValidatorProposal.sol | A new proposal type that allows setting a signature validator for a given signature hash or simply validating the hash |                                                                       |
| contracts/signature-validators/OffChainSignatureValidator.sol | Validator contract that reconstructs a message and ensures it's a plaintext message. It then returns valid if the signer has sufficient voting power or membership in the party |                                                                       |
| contracts/utils/Implementation.sol                | A contract that provides helper functions for implementation contracts used from a proxy  |                                                                       |

In the "Libraries used" column I left some empty where there are no libraries mentioned.


**Key Contracts**

- `PartyGovernance` - Implements group membership, voting power, proposals, and governance logic. Inherits token logic from `PartyGovernanceNFT`.

- `PartyGovernanceNFT` - Implements ERC721 token functionality for Party membership NFTs. Inherits governance logic from `PartyGovernance`.

- `ProposalExecutionEngine` - Contains logic for executing proposals like listing NFTs on platforms. `Party` delegatecalls into this.

- `ProposalStorage` - Shared storage accessed by `Party` and `ProposalExecutionEngine`.

- `InitialETHCrowdfund` - Used to crowdfund and initialize a new Party.

**Mechanisms**

- **Proposal Lifecycle** - Members create proposals like executing arbitrary calls. Proposals go through stages like Voting => Passed => Ready. Once Ready any member can execute.

**Proposals**

| Stage | Description |
|-|-|  
| Voting | - Proposal created via `propose()` <br>- Members vote via `accept()` <br>- Hosts can veto|
| Passed | - Proposal passes vote threshold <br>- Execution delay starts|
| Ready | - Execution delay over <br>- Proposal can now be executed| 
| InProgress | - `execute()` called <br>- Further executions potentially needed|
| Completed | - Proposal fully executed |

Proposals progress through predefined stages enforced by the `Party` contract. This prevents premature execution and enables multi-transaction proposals.

The `ProposalExecutionEngine` contains the logic for each proposal type like listing NFTs or making arbitrary calls. `Party` delegatecalls into it to execute proposals.

## Change Log

The changelog lists new features, enhancements, and fixes added to the Party Protocol smart contracts.

**Adding ERC1271 Support**

- Motivation: Allow Parties to integrate with web3 apps requiring signatures from their on-chain address. Also enable signing messages via proposals.

- Implementation: `ProposalExecutionEngine` now implements ERC1271. `executeProposal` validates signatures. `OffChainSignatureValidator` added to validate signatures with voting power checks.

- Scenarios: 
    - Members propose and pass a transaction requiring an ERC1271 signature from the Party address. The signature is provided in the proposal call data.

    - A web3 app requires members sign a message proving membership in a specific Party. The app verifies the signature validity via ERC1271.

**Modifying Governance Settings** 

- Motivation: Allow governance parameters like voting duration to be changed by the Party after initialization.

- Implementation: `SetGovernanceParameterProposal` added. Calls can modify governance values stored in `ProposalStorage`.

- Scenarios:
    - Party members propose reducing the voting period for quicker governance.

    - A Party proposes increasing the proposal acceptance threshold to require broader consensus.

**Skipping Veto Period**

- Motivation: Speed up execution if all Hosts have already accepted the proposal.

- Implementation: Additional logic checks if all Hosts accepted before veto period ends. If so, status immediately changes to Ready.

- Scenarios:
    - A proposal receives unanimous positive votes including all Hosts. The execution delay is skipped.

**Voting Power** - Each Party membership NFT has voting power. Members delegate voting power to vote on proposals.

<img src="https://i.ibb.co/6XYf7Kk/Voting-Power.png" width="500" />

- Each Party membership NFT confers voting power
- Members can delegate voting power to others
- `Party` contract tracks voting power snapshots  
- Snapshots used to determine voting power at time of proposals
- Enables fluid governance as voting power can be re-delegated  

Tracking voting power snapshots provides flexibility compared to a static system. However, complexity is increased. Voting power accounting logic should be thoroughly reviewed.

**Distributions** - Mechanism for Party to distribute assets to members proportionally to voting power.

<img src="https://i.ibb.co/Q9WsX4p/Distributions.png" width="500" />

- Party can distribute assets (ETH, ERC20) to members
- Distributed proportional to member's voting power
- Configurable to require a proposal or be executable by any member
- Provides incentives and aligns members

Requiring a proposal for distributions provides oversight at the cost of speed. Distribution calculations should be thoroughly reviewed.

**Rage Quit** - Members can burn their NFT and withdraw a proportional share of assets. 

- Members can burn NFT and withdraw proportional share of assets 
- Provides an escape hatch if member becomes dissatisfied
- Configurable whether rage quit is enabled or not
- Withdrawal calculations must be carefully reviewed

**Architecture**

The Party contract acts as the main governance interface and coordinates all logic. It inherits:

- Token logic from PartyGovernanceNFT 
- Governance logic from PartyGovernance

This separation of concerns provides flexibility.

Proposal execution logic lives in the `ProposalExecutionEngine`. The Party contract `delegatecalls` into this contract to execute proposals. This enables upgrading the execution logic independently from the Party itself.

`ProposalStorage` provides shared storage accessed by both Party and `ProposalExecutionEngine`. This avoids duplication.

The `IGlobals` contract provides configurable addresses for components like the `ProposalExecutionEngine`. While relying on a central source of truth introduces some centralization risks, it enables easy upgrades to the ecosystem. The immutability of IGlobals mitigates some risks.

- `Party` acts as the governance interface and coordinates all logic.

- `Party` inherits token logic from `PartyGovernanceNFT` and governance logic from `PartyGovernance`.

- `ProposalExecutionEngine` contains modular proposal logic that `Party` delegatecalls into.

- `ProposalStorage` enables sharing storage between `Party` and `ProposalExecutionEngine`.

- `IGlobals` provides global configuration. Addresses are trusted.

- Gatekeepers like `AllowListGateKeeper` manage access.

- `Implementation` pattern used for upgradability via proxies.

**Key Mechanisms**

**Proposals**

Members create proposals like executing arbitrary calls or changing parameters. Proposals progress through stages enforced by the Party contract:

- Voting - Members vote on the proposal 
- Passed - Proposal gathers enough votes
- Ready - After execution delay passes, proposal can be executed
- InProgress - Being executed over multiple transactions
- Completed - Execution finished

This lifecycle prevents premature execution and enables multi-step proposals.

**Voting Power**

Each Party membership NFT confers voting power to its holder. Members can delegate their voting power to others to vote on their behalf. This enables flexible and fluid governance.

The Party contract tracks snapshots of voting power over time to determine voting power at the time of proposals.

**Distributions**

The Party can distribute assets like ETH or ERC20 tokens to members proportional to their voting power. This incentivizes participation and alignment.

Distributions can be configured to either require a proposal or be executable by any member with voting power. The latter increases flexibility but reduces oversight.

**Rage Quit** 

Members can burn their NFT and withdraw a proportional share of assets based on voting power. This provides an escape hatch if members become dissatisfied with the Party's governance. The ability to rage quit can be configured on/off.

**Proposals**

1. Member calls `propose()` with details of proposal

2. Proposal enters `Voting` stage 
    - Other members vote via `accept()`
    - Hosts can veto proposal

3. If proposal passes vote threshold, enters `Passed` stage
    - `executionDelay` countdown starts 

4. After `executionDelay` passes, proposal enters `Ready` stage
   - Any member can now execute via `execute()`

5. On first call to `execute()`:
   - Proposal enters `InProgress` stage
   - `Party` delegatecalls `ProposalEngine` to run execution logic
   - `InProgress` prevents other proposals from executing

6. If multi-step proposal:
   - `ProposalEngine` returns non-empty `nextProgressData` 
   - Further `execute()` calls required with `nextProgressData`

7. When `ProposalEngine` returns empty `nextProgressData`:
   - Proposal enters `Completed` stage
   - No further execution possible

**Voting Power**

1. Each Party membership NFT confers intrinsic voting power

2. Members can delegate voting power via `delegateVotingPower()`

3. `Party` contract tracks snapshots of voting power

4. On proposal, voting power at time of proposal determined from snapshots 

5. Voting power can be re-delegated at any time

**Distributions**

1. Member calls `distribute()` on `Party` 

2. Party transfers assets to `TokenDistributor`

3. `TokenDistributor` mints member tokens proportional to voting power

4. Optional: Require distribution proposal to pass before executing

**Rage Quit** 

1. Member calls `rageQuit()`  

2. Member burns NFT

3. `Party` transfers proportional share of assets to member based on voting power

4. Configurable whether rage quit enabled 

## Analysis

The overall architecture promotes separation of concerns and upgradeability while keeping the Party contract as the coordinator. 

Relying on `IGlobals` for configuration introduces some centralization risks. For example, a compromized `IGlobals` could point critical addresses like the `ProposalEngine` to a malicious contract. Additional trust assumptions are placed on the initial setters of `IGlobals`.

The usage of `delegatecall` to the `ProposalEngine` warrants extra care to ensure state mutations are valid. Potential reentrancy issues should be evaluated thoroughly.

No major systemic issues identified with the core mechanisms like voting and distributions. However, detailed analysis of the voting power accounting and rage quit calculations is recommended. 

Beyond the core protocol, integrations with external contracts like NFT platforms carry additional risks and trust assumptions. I'd recommend thoroughly reviewing the related proposal types is recommended.





### Time spent:
11 hours