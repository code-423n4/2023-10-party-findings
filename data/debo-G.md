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