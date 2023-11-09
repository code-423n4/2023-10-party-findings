## [G-01] MULTIPLICATION/DIVISION BY TWO SHOULD USE BIT SHIFTING
**Impact**
x * 2 is equal to x << 1 and x / 2 is equal to x >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas.
**Remediation**
It is recommended to use left and right shift instead of multiplying and dividing by 2 to save some gas.
**Location**
```txt
contracts/party/PartyGovernance.sol#L434-L434
```
## [G-02] DEFINE CONSTRUCTOR AS PAYABLE
**Impact**
Developers can save around 10 opcodes and some gas if the constructors are defined as payable.
However, it should be noted that it comes with risks because payable constructors can accept ETH during deployment.
**Remediation**
It is suggested to mark the constructors as payable to save some gas. Make sure it does not lead to any adverse effects in case an upgrade pattern is involved.
**Locations**
```txt
contracts/crowdfund/ETHCrowdfundBase.sol#L134-L136
contracts/crowdfund/InitialETHCrowdfund.sol#L99-L99
contracts/proposals/ProposalExecutionEngine.sol#L107-L118
```
## [G-03] CHEAPER INEQUALITIES IN REQUIRE()
**Impact**
The contract was found to be performing comparisons using inequalities inside the require statement. 
When inside the require statements, non-strict inequalities (>=, <=) are usually costlier than strict equalities (>, <).
**Remediation**
It is recommended to go through the code logic, and, if possible, modify the non-strict inequalities with the strict ones to save ~3 gas as long as the logic of the code is not affected.
**Locations**
```txt
contracts/proposals/ProposalExecutionEngine.sol#L314-L314
```
## [G-04] CHEAPER INEQUALITIES IN IF()
**Impact**
The contract was found to be doing comparisons using inequalities inside the if statement.
When inside the if statements, non-strict inequalities (>=, <=) are usually cheaper than the strict equalities (>, <).
**Remediation**
It is recommended to go through the code logic, and, if possible, modify the strict inequalities with the non-strict ones to save ~3 gas as long as the logic of the code is not affected.
**Locations**
```txt
contracts/crowdfund/ETHCrowdfundBase.sol#L142-L142
contracts/crowdfund/ETHCrowdfundBase.sol#L148-L148
contracts/crowdfund/ETHCrowdfundBase.sol#L223-L223
contracts/crowdfund/ETHCrowdfundBase.sol#L256-L256
contracts/crowdfund/ETHCrowdfundBase.sol#L263-L263
contracts/crowdfund/ETHCrowdfundBase.sol#L286-L286
contracts/crowdfund/ETHCrowdfundBase.sol#L324-L324
contracts/party/PartyGovernanceNFT.sol#L179-L179
contracts/party/PartyGovernanceNFT.sol#L216-L216
contracts/signature-validators/OffChainSignatureValidator.sol#L73-L73
contracts/party/PartyGovernance.sol#L277-L277
contracts/party/PartyGovernance.sol#L280-L280
contracts/party/PartyGovernance.sol#L302-L302
contracts/crowdfund/InitialETHCrowdfund.sol#L141-L141
contracts/crowdfund/InitialETHCrowdfund.sol#L224-L224
contracts/crowdfund/InitialETHCrowdfund.sol#L331-L331
contracts/proposals/ProposalExecutionEngine.sol#L303-L303
```
## [G-05] ARRAY LENGTH CACHING
**Impact**
During each iteration of the loop, reading the length of the array uses more gas than is necessary. 
In the most favourable scenario, in which the length is read from a memory variable, storing the array length in the stack can save about 3 gas per iteration. 
In the least favourable scenario, in which external calls are made during each iteration, the amount of gas wasted can be significant.
**Locations**
```txt
The following array was detected to be used inside loop without caching it's value in memory: tokenIds.
contracts/party/PartyGovernanceNFT.sol#L272-L301

The following array was detected to be used inside loop without caching it's value in memory: govOpts.
contracts/party/PartyGovernance.sol#L305-L307

The following array was detected to be used inside loop without caching it's value in memory: votingPowers.
contracts/crowdfund/InitialETHCrowdfund.sol#L260-L269
```
**Recommendations**
Consider storing the array length of the variable before the loop and use the stored length instead of fetching it in each iteration.
## [G-06] STORAGE VARIABLE CACHING IN MEMORY
**Impact**
`contracts/crowdfund/ETHCrowdfundBase.sol#L112-L112`
The contract ETHCrowdfundBase is using the state variable maxTotalContributions multiple times in the function _processContribution.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/crowdfund/ETHCrowdfundBase.sol#L114-L114`
The contract ETHCrowdfundBase is using the state variable totalContributions multiple times in the function _processContribution.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/crowdfund/ETHCrowdfundBase.sol#L131-L131`
The contract ETHCrowdfundBase is using the state variable delegationsByContributor multiple times in the function _processContribution.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/crowdfund/ETHCrowdfundBase.sol#L92-L92`
The contract ETHCrowdfundBase is using the state variable party multiple times in the function _finalize.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/crowdfund/ETHCrowdfundBase.sol#L104-L104`
The contract ETHCrowdfundBase is using the state variable fundingSplitPaid multiple times in the function sendFundingSplit.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernanceNFT.sol#L49-L49`
The contract PartyGovernanceNFT is using the state variable mintedVotingPower multiple times in the function mint.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernanceNFT.sol#L57-L57`
The contract PartyGovernanceNFT is using the state variable votingPowerByTokenId multiple times in the function increaseVotingPower.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernanceNFT.sol#L57-L57`
The contract PartyGovernanceNFT is using the state variable votingPowerByTokenId multiple times in the function _burnAndUpdateVotingPower.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernanceNFT.sol#L35-L35`
The contract PartyGovernanceNFT is using the state variable DISABLE_RAGEQUIT_PERMANENTLY multiple times in the function setRageQuit.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernanceNFT.sol#L55-L55`
The contract PartyGovernanceNFT is using the state variable rageQuitTimestamp multiple times in the function setRageQuit.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernanceNFT.sol#L35-L35`
The contract PartyGovernanceNFT is using the state variable DISABLE_RAGEQUIT_PERMANENTLY multiple times in the function rageQuit.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernanceNFT.sol#L38-L38`
The contract PartyGovernanceNFT is using the state variable ETH_ADDRESS multiple times in the function rageQuit.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernanceNFT.sol#L55-L55`
The contract PartyGovernanceNFT is using the state variable rageQuitTimestamp multiple times in the function rageQuit.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/signature-validators/OffChainSignatureValidator.sol#L22-L22`
The contract OffChainSignatureValidator is using the state variable signingThersholdBps multiple times in the function setSigningThresholdBps.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernance.sol#L206-L206`
The contract PartyGovernance is using the state variable isHost multiple times in the function abdicateHost.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernance.sol#L210-L210`
The contract PartyGovernance is using the state variable numHosts multiple times in the function propose.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernance.sol#L212-L212`
The contract PartyGovernance is using the state variable _proposalStateByProposalId multiple times in the function propose.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernance.sol#L191-L191`
The contract PartyGovernance is using the state variable _GLOBALS multiple times in the function cancel.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernance.sol#L208-L208`
The contract PartyGovernance is using the state variable delegationsByVoter multiple times in the function _adjustVotingPower.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
`contracts/party/PartyGovernance.sol#L186-L186`
The contract PartyGovernance is using the state variable UINT40_HIGH_BIT multiple times in the function _getProposalStatus.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).
**Remediation**
Storage variables read multiple times inside a function should instead be cached in the memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.
## [G-07] Don't Initialize Variables with Default Value
**Impact**
Relying on default values may lead to unintended consequences if the default values are not explicitly defined. 
This can result in unexpected behavior, especially if the default value is 0.
Initializing storage variables explicitly during contract deployment can save gas by avoiding unnecessary storage writes.
**Locations**
```
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::379 => for (uint i = 0; i < authoritiesLength - 1; ++i) {
2023-10-party/contracts/party/PartyGovernance.sol::305 => for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
2023-10-party/contracts/party/PartyGovernance.sol::432 => uint256 low = 0;
2023-10-party/contracts/party/PartyGovernance.sol::1054 => uint256 flags = 0;
```
## [G-08] Cache Array Length Outside of Loop
**Impact**
If the array length changes dynamically within the loop, caching it outside might lead to incorrect results or unexpected behaviour. 
This is especially relevant if elements are added or removed from the array inside the loop.
Caching the array length outside of a loop can result in gas savings. 
This is because, in Solidity, accessing the length of an array inside a loop can be more gas-intensive as the length is recalculated in every iteration.
**Locations**
```sol
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::70 => // The contribution amounts in wei. The length of this array must be
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::71 => // equal to the length of `tokenIds`.
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::88 => // The contribution amounts in wei. The length of this array must be
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::89 => // equal to the length of `recipients`.
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::207 => uint256 numContributions = args.tokenIds.length;
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::258 => votingPowers = new uint96[](args.recipients.length);
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::260 => for (uint256 i; i < args.recipients.length; ++i) {
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::354 => uint256 numRefunds = tokenIds.length;
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::377 => uint256 authoritiesLength = opts.authorities.length + 1;
2023-10-party/contracts/party/PartyGovernance.sol::295 => numHosts = uint8(govOpts.hosts.length);
2023-10-party/contracts/party/PartyGovernance.sol::302 => if (govOpts.hosts.length > type(uint8).max) {
2023-10-party/contracts/party/PartyGovernance.sol::305 => for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
2023-10-party/contracts/party/PartyGovernance.sol::431 => uint256 high = snaps.length;
2023-10-party/contracts/party/PartyGovernance.sol::900 => return nextProgressData.length == 0;
2023-10-party/contracts/party/PartyGovernance.sol::910 => uint256 snapsLength = snaps.length;
2023-10-party/contracts/party/PartyGovernance.sol::1031 => uint256 n = voterSnaps.length;
2023-10-party/contracts/party/PartyGovernance.sol::1047 => uint256 n = voterSnaps.length;
2023-10-party/contracts/party/PartyGovernance.sol::1141 => if (preciousTokens.length != preciousTokenIds.length) {
2023-10-party/contracts/party/PartyGovernanceNFT.sol::102 => for (uint256 i; i < authorities.length; ++i) {
2023-10-party/contracts/party/PartyGovernanceNFT.sol::272 => for (uint256 i; i < tokenIds.length; ++i) {
2023-10-party/contracts/party/PartyGovernanceNFT.sol::350 => if (tokenIds.length == 0) revert NothingToBurnError();
2023-10-party/contracts/party/PartyGovernanceNFT.sol::375 => uint256[] memory withdrawAmounts = new uint256[](withdrawTokens.length);
2023-10-party/contracts/party/PartyGovernanceNFT.sol::378 => for (uint256 i; i < withdrawTokens.length; ++i) {
2023-10-party/contracts/party/PartyGovernanceNFT.sol::391 => for (uint256 j; j < tokenIds.length; ++j) {
2023-10-party/contracts/party/PartyGovernanceNFT.sol::408 => for (uint256 i; i < withdrawTokens.length; ++i) {
2023-10-party/contracts/proposals/ProposalExecutionEngine.sol::131 => if (initializeData.length == 0) return;
2023-10-party/contracts/proposals/ProposalExecutionEngine.sol::169 => if (params.progressData.length != 0) {
2023-10-party/contracts/proposals/ProposalExecutionEngine.sol::197 => if (nextProgressData.length == 0) {
2023-10-party/contracts/proposals/ProposalExecutionEngine.sol::303 => if (proposalData.length < 4) {
2023-10-party/contracts/proposals/ProposalExecutionEngine.sol::307 => // By reading 4 bytes into the length prefix, the leading 4 bytes
```
## [G-09] Long Revert Strings
1. Revert Operation Gas Cost:
Long Revert Strings:

Including long revert strings in require or assert statements can significantly increase gas consumption.
Longer strings require more gas to store and execute.
Short Revert Strings:

Using shorter revert strings reduces gas costs, as less gas is needed to store and execute them.
2. Contract Deployment:
Long Revert Strings:

During contract deployment, specifying long revert strings increases the deployment gas cost.
This can impact the overall cost of deploying the contract to the blockchain.
Short Revert Strings:

Using concise and informative revert strings reduces deployment gas costs.
3. Execution of Revert Statements:
Long Revert Strings:

Executing revert statements with long strings consumes more gas.
Transactions that trigger reverts will incur higher costs.
Short Revert Strings:

Shorter strings reduce the gas cost of reverting, making transactions more cost-effective.
4. Contract Interactions:
Long Revert Strings:

When interacting with other contracts, sending a transaction that triggers a revert with a long string can result in higher gas costs.
Short Revert Strings:

Using shorter strings in revert statements can optimize gas usage in contract interactions.
5. Code Size:
Long Revert Strings:

Longer strings contribute to larger contract bytecode size, potentially impacting the gas cost of deploying and interacting with the contract.
Short Revert Strings:

Using shorter strings helps to keep the bytecode size smaller, reducing gas costs.
6. User Experience:
Long Revert Strings:

While informative error messages are essential for developers, excessively long strings may not significantly improve the end-user experience.
Short Revert Strings:

Short and clear error messages provide necessary information without sacrificing gas efficiency.
**Locations**
```sol
2023-10-party/contracts/crowdfund/InitialETHCrowdfund.sol::11 => import { MetadataProvider } from "../renderers/MetadataProvider.sol";
2023-10-party/contracts/party/PartyGovernance.sol::4 => import { ITokenDistributor } from "../distribution/ITokenDistributor.sol";
2023-10-party/contracts/party/PartyGovernance.sol::5 => import { ReadOnlyDelegateCall } from "../utils/ReadOnlyDelegateCall.sol";
2023-10-party/contracts/party/PartyGovernance.sol::16 => import { IProposalExecutionEngine } from "../proposals/IProposalExecutionEngine.sol";
2023-10-party/contracts/party/PartyGovernanceNFT.sol::6 => import "openzeppelin/contracts/interfaces/IERC2981.sol";
2023-10-party/contracts/proposals/ProposalExecutionEngine.sol::7 => import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";
2023-10-party/contracts/proposals/ProposalExecutionEngine.sol::11 => import "./ListOnOpenseaAdvancedProposal.sol";
2023-10-party/contracts/proposals/ProposalExecutionEngine.sol::19 => import { SetSignatureValidatorProposal } from "./SetSignatureValidatorProposal.sol";
2023-10-party/contracts/proposals/ProposalExecutionEngine.sol::20 => import { SetGovernanceParameterProposal } from "./SetGovernanceParameterProposal.sol";
2023-10-party/contracts/proposals/ProposalStorage.sol::41 => uint256(keccak256("ProposalStorage.SharedProposalStorage"));
2023-10-party/contracts/proposals/SetSignatureValidatorProposal.sol::4 => import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";
2023-10-party/contracts/proposals/SetSignatureValidatorProposal.sol::14 => uint256(keccak256("SetSignatureValidatorProposal.Storage"));
```