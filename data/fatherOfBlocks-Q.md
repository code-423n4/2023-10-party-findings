**contracts/proposals/SetGovernanceParameterProposal.sol**
- L54 - In the _executeSetGovernanceParameter() function, flow controllers are used validating that proposalData.passThresholdBps > 10000, 
but it is not explained why those numbers are.
This should be more explanatory, giving it a name according to these values ​​and not just using the numbers without an explanation.


**contracts/signature-validators/OffChainSignatureValidator.sol**
- L74 - A division is made by totalVotingPower, without validating if this variable is != 0, this is important since it could generate an unhandled exception.


**contracts/party/PartyGovernance.sol**
- L1115/1134 - A division is made by totalVotingPower, without validating if this variable is != 0, this is important since it could generate an unhandled exception.
