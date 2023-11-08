# 🛠️ Analysis - Party Protocol Audit
### Summary
| List |Head |Details|
|:--|:----------------|:------|
|a) |The approach I followed when reviewing the code | Stages in my code review and analysis |
|b) |Analysis of the code base | What is unique? How are the existing patterns used? "Solidity-metrics" was used  |
|c) |Test analysis | Test scope of the project and quality of tests |
|d) |Security Approach of the Project | Audit approach of the Project |
|e) |Other Audit Reports and Automated Findings | What are the previous Audit reports and their analysis |
|f) |Packages and Dependencies Analysis | Details about the project Packages |
|g) |Other recommendations | What is unique? How are the existing patterns used? |
|h) |New insights and learning from this audit | Things learned from the project |



## a) The approach I followed when reviewing the code

First, by examining the scope of the code, I determined my code review and analysis strategy.
https://github.com/code-423n4/2023-10-party

Accordingly, I analyzed and audited the subject in the following steps;

| Number |Stage |Details|Information|
|:--|:----------------|:------|:------|
|1|Compile and Run Test|[Installation](https://github.com/code-423n4/2023-10-party#quickstart-command)|Test and installation structure is simple, cleanly designed|
|2|Architecture Review| [Party](https://docs.partydao.org/) |Provides a basic architectural teaching for General Architecture|
|3|Graphical Analysis  |Graphical Analysis with [Solidity-metrics](https://github.com/ConsenSys/solidity-metrics)|A visual view has been made to dominate the general structure of the codes of the project.|
|4|Slither Analysis  | [Slither Report](https://github.com/crytic/slither)| The project does not currently have a slither result, a slither control was created from initial|
|5|Test Suits|[Tests](https://github.com/code-423n4/2023-10-party#testing)|In this section, the scope and content of the tests of the project are analyzed.|
|6|Manuel Code Review|[Scope](https://github.com/code-423n4/2023-10-party#scoping-details)||
|7|Infographic|[Figma](https://www.figma.com/)|I made Visual drawings to understand the hard-to-understand mechanisms|
|8|Special focus on Areas of  Concern|[Areas of Concern](https://github.com/code-423n4/2023-10-party#areas-of-interest)||

## b) Analysis of the code base

The most important summary in analyzing the code base is the stacking of codes to be analyzed.
In this way, many predictions can be made, including the difficulty levels of the contracts, which one is more important for the auditor, the features they contain that are important for security (payable functions, uses assembly, etc.), the audit cost of the project, and the time to be allocated to the audit;
Uses Consensys Solidity Metrics
-  **Lines:** total lines of the source unit
-  **nLines:** normalized lines of the source unit (e.g. normalizes functions spanning multiple lines)
-  **nSLOC:** normalized source lines of code (only source-code lines; no comments, no blank lines)
-  **Comment Lines:** lines containing single or block comments
-  **Complexity Score:** a custom complexity score derived from code statements that are known to introduce code complexity (branches, loops, calls, external interfaces, ...)

![image](https://github.com/code-423n4/2023-10-party/assets/104318932/bfb0ac99-f77d-40e7-a036-3f7173124089)


</br>
</br>



![image](https://github.com/code-423n4/2023-10-party/assets/104318932/0e38a7bf-0975-4667-87c4-47656eeff039)



## Start Point
![image](https://github.com/code-423n4/2023-10-party/assets/104318932/0b928c5e-5d90-4c1d-8ebd-658100b6b4da)

## PartyFactory.sol
Factory used to deploy new proxified `Party` instances;
![image](https://github.com/code-423n4/2023-10-party/assets/104318932/f5581b23-f0f5-42d2-8612-d912cab3f3c3)

![image](https://github.com/code-423n4/2023-10-party/assets/104318932/6077772f-196c-4023-a5d1-9fa3ec896008)

## Party.sol

Initializer to be delegatecalled by `Proxy` constructor. Will revert if called outside the constructor.
initData Options used to initialize the party governance.
![image](https://github.com/code-423n4/2023-10-party/assets/104318932/fdd932a1-6469-41b8-b3dc-65d82f4605b1)

![image](https://github.com/code-423n4/2023-10-party/assets/104318932/69ba039d-2b18-476e-a4b0-a6c2020b554c)

## PartyGovernanceNFT.sol
Initialize storage for proxy contracts
![image](https://github.com/code-423n4/2023-10-party/assets/104318932/4d9ed43b-7e2c-4911-954c-9bc78cb42f3e)

![image](https://github.com/code-423n4/2023-10-party/assets/104318932/02a1c82a-fbb6-4168-a02b-3dcc8aa15cf2)
</br>
## PartyGovernance.sol

enum ProposalStatus : States a proposal can be in;
![image](https://github.com/code-423n4/2023-10-party/assets/104318932/ad3b3d9c-c2cd-454f-b7bf-07dbda85fb43)

</br>

Initialize storage for proxy contracts and initialize the proposal execution engine

![image](https://github.com/code-423n4/2023-10-party/assets/104318932/51757d6c-16c4-42b9-91da-de9ce055c869)

![image](https://github.com/code-423n4/2023-10-party/assets/104318932/d79eba5a-2cff-4e2d-9108-eb252d1a6f5a)

</br>

## ProposalExecutionEngine.sol

![image](https://github.com/code-423n4/2023-10-party/assets/104318932/2c2e2eb5-c73a-43b6-a686-115cb8aeafc1)

----------------------------------------------------------------------------------------

</br>

![image](https://github.com/code-423n4/2023-10-party/assets/104318932/e00df53e-0286-4531-9c8a-c78db6bf4daf)

----------------------------------------------------------------------------------------

</br>

![image](https://github.com/code-423n4/2023-10-party/assets/104318932/950f83e2-b08c-4d3b-9e43-f6d50c7ba4f3)

----------------------------------------------------------------------------------------

</br>

## InitialETHCrowdfund.sol
A crowdfund for raising the initial funds for new parties. Unlike other crowdfunds that are started for the purpose of acquiring NFT(s), this crowdfund simply bootstraps a party with funds and lets its members coordinate on what to do with it after.

Initializer to be delegatecalled by `Proxy` constructor. Will revert if called outside the constructor.
InitialETHCrowdfund.initialize() : 

![image](https://github.com/code-423n4/2023-10-party/assets/104318932/726ba219-e3e4-4159-8872-d9f758d74cf6)



</br>
</br>

## c) Test analysis
### What did the project do differently? ;
-   1) It can be said that the developers of the project did a quality job, there is a test structure consisting of tests with quality content.




### What could they have done better?


-  1) In order to understand the test scenarios and develop more effective test scenarios, the following bob, alice and other roles are can be defined one by one, in this way role definitions increase the quality and readability in tests

```solidity

 // Sample labels
vm.label(bob, 'bob');
vm.label(alice, 'alice');
vm.label(DEPLOYER, 'deployer');
vm.label(USDE_OWNER, 'usde owner');
vm.label(POOL_PROXY, 'lending pool');
```






## d) Security Approach of the Project

### Successful current security understanding of the project;

1 - First they did the main audit from Code4rena and resolved all the security concerns in the report 


### What the project should add in the understanding of Security;

1- By distributing the project to testnets, ensuring that the audits are carried out in onchain audit. (This will increase coverage)

2- Add On-Chain Monitoring System; If On-Chain Monitoring systems such as Forta are added to the project, its security will increase.

For example ; This bot tracks any DEFI transactions in which wrapping, unwrapping, swapping, depositing, or withdrawals occur over a threshold amount. If transactions occur with unusually high token amounts, the bot sends out an alert. https://app.forta.network/bot/0x7f9afc392329ed5a473bcf304565adf9c2588ba4bc060f7d215519005b8303e3

3- After the Code4rena audit is completed and the project is live, I recommend the audit process to continue, projects like immunefi do this. https://immunefi.com/





## e) Other Audit Reports and Automated Findings 

**Automated Findings:**
https://github.com/code-423n4/2023-10-party/blob/main/bot-report.md


**Other Audit Reports : 
[2023-04-party](https://github.com/code-423n4/2023-04-party-findings/issues?q=label%3A%22sponsor+acknowledged%22+label%3A%222+%28Med+Risk%29%22%2C%223+%28High+Risk%29%22+)
[2023-05-party](https://github.com/code-423n4/2023-05-party-findings/issues?q=label%3A%22sponsor+acknowledged%22+label%3A%222+%28Med+Risk%29%22%2C%223+%28High+Risk%29%22+)
[2023-09-party](https://github.com/code-423n4/2022-09-party-findings/issues?q=label%3A%22sponsor+acknowledged%22+label%3A%222+(Med+Risk)%22%2C%223+(High+Risk)%22+)



##  f) Packages and Dependencies Analysis 📦

| Package                                                                                                                                     | Version                                                                                                               | Usage in the project                               | Audit Recommendation                                                                                                             |
| ------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| [`openzeppelin`](https://www.npmjs.com/package/@openzeppelin/contracts)         | [![npm](https://img.shields.io/npm/v/@openzeppelin/contracts.svg)](https://www.npmjs.com/package/@openzeppelin/contracts)     |               IERC1271.sol, IERC2981.sol, Strings.sol |-  Version `5.0.0` is used by the project,  this is newest version ✅   |







## g) Other recommendations

✅ The use of assembly in project codes is very low, I especially recommend using such useful and gas-optimized code patterns; https://github.com/dragonfly-xyz/useful-solidity-patterns/tree/main/patterns/assembly-tricks-1

✅A good model can be used to systematically assess the risk of the project, for example this modeling is recommended; https://www.notion.so/Smart-Contract-Risk-Assessment-3b067bc099ce4c31a35ef28b011b92ff#7770b3b385444779bf11e677f16e101e

✅`ProposalExecutionEngine.isValidSignature()` function uses tx.origin pattern, never use tx. origin for authorization, another contract can have a method which will call your contract (where the user has some funds for instance) and your contract will authorize that transaction as your address is in tx. origin .
A serious other problem for a contract depending on tx.origin is that your contract will not work correctly with multisig wallets (or any other smart contract).

```solidity
contracts\proposals\ProposalExecutionEngine.sol:
  223  
  224  
  225:     function isValidSignature(bytes32 hash, bytes memory signature) external view returns (bytes4) {
  226:         IERC1271 validator = getSignatureValidatorForHash(hash);
  227:         if (address(validator) == address(1)) {
  228:             // Signature set by party to be always valid
  229:             return IERC1271.isValidSignature.selector;
  230:         }
  231:         if (address(validator) != address(0)) {
  232:             return validator.isValidSignature(hash, signature);
  233:         }
  234:         if (tx.origin == address(0)) {
  235:             validator = getSignatureValidatorForHash(0);
  236:             if (address(validator) == address(0)) {
  237:                 // Use global off-chain signature validator
  238:                 validator = IERC1271(
  239:                     _GLOBALS.getAddress(LibGlobals.GLOBAL_OFF_CHAIN_SIGNATURE_VALIDATOR)
  240:                 );
  241:             }
  242:             return validator.isValidSignature(hash, signature);
  243:         }
  244:         return 0;
  244      }
```



## h) New insights and learning from this audit 

🔎 1. Crowdfunds: Understanding how crowdfunds work, including the various types of crowdfund contracts and their specific behaviors, can inspire ideas for creating modular and scalable smart contract systems.

🔎 2. Governance Mechanisms in DAOs: Learning about how Parties function, including how members vote and make proposals, could provide insights into effective decentralized governance structures and how to implement them on the blockchain.

🔎 3. Tokenomics and NFT Utility: Analyzing the role of Contribution Cards and Party Cards can give a deeper understanding of how NFTs can be used beyond just art or collectibles, particularly in representing governance rights and membership within a digital community.

🔎 4. Security and Access Control: The AllowListGateKeeper and TokenGateKeeper contracts demonstrate methods of restricting participation in blockchain projects, which could inspire best practices for security and access control in smart contracts.

🔎 5. Funding and Treasury Management: Insights into how crowdfunds can bootstrap a treasury and how Parties manage distributions and the Rage Quit mechanism could inform on sustainable financial management within a blockchain project or DAO.


### Time spent:
17 hours