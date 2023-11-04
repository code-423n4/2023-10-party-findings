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