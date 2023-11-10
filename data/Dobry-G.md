# Gas Optimizations
## Use unchecked for operations that will not overflow

##### ETHCrowdfundBase.sol
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol

266: amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
270: votingPower = (amount * exchangeRateBps) / 1e4;
287: amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
329: uint96 newVotingPower = (totalContributions_ * exchangeRateBps) / 1e4;
355: splitAmount = (totalContributions * fundingSplitBps_) / 1e4;

##### PartyGovernance.sol
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol

434: uint256 mid = (low + high) / 2;
115: uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;

##### PartyGovernanceNFT.sol

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol

157: totalVotingPower == 0 ? 0 : (votingPowerByTokenId[tokenId] * 1e18) / totalVotingPower;
413: uint256 fee = (amount * feeBps_) / 1e4;

## Public functions not called by the contract should be declared external

##### ETHCrowdfundBase.sol

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol

278: function convertVotingPowerToContribution(

## Operator >=/<= costs less gas than operator >/<

###Non-strict inequalities (>=) are cheaper than strict ones (>). This is due to some supplementary checks (ISZERO, 3 gas)).

####non-strict inequalities will save you 15–20 gas.

##### ETHCrowdfundBase.sol

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol

239: if (refundAmount > 0) {
263: if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
286: if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
324: if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {

##### ProposalExecutionEngine.sol

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol

303: if (proposalData.length < 4) {

## Use named variables in function returns

##### PartyGovernance.sol

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol

### A return variable is already given
### There is no need to return it at the end of the function body

908:  internal view returns (VotingPowerSnapshot memory snap) {

### Not using the named return variables when a function returns, wastes deployment gas

426:  public view returns (uint256 index) {

##### ProposalExecutionEngine.sol

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol

### Function is not returning the variable “id”, so it should be removed from line 141

141: function getCurrentInProgressProposalId() external view returns (uint256 id) {