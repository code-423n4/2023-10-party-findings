# Analysis of the Party Protocol Codebase


## Approach 

First, the auditing process started with getting the high-level understanding of the protocol and its key contracts (the ones that have the most SLOC) and extending to the smaller ones. Then the more detailed approach was taken and specific areas of concern (such as delegation, proper calculation of voting power) were thoroughly checked to make sure that the main functionality of the protocol works as it should. During this period, some questions were discussed with developers to make sure that my understanding of the functionality is actually right and I can continue searching for vulnerabilities. In conclusion, I repeatedly looked through the main contracts to make sure that I didn't miss anything and tried to create more complex attack vectors based on the complete understanding.


## Architecture overview

`InitialETHCrowdfund.sol` - the contract where users may contribute and get their NFT minted with `votingPower` attached or increase `votingPower` of already existing tokenId. Also, the initial contract that starts the party by calling `_createParty()`.

`ETHCrowdfundBase.sol` - the contract that the `InitialETHCrowdfund.sol` relies on. It contains several important functions such as `_processContribution()` that calculates the `votingPower` of the user based on his contribution in ETH and `_finalize()` function that actually finalizes the crowdfund process and sends ETH to the party.

`PartyGovernance.sol` - the most important contract that contains all the governance functionality to create proposals, accept (vote for them) them, veto, cancel and, finally, execute them by making a call to the `ProposalExecutionEngine.sol`. Additionally, the contract has the logic to create token distribution between party members by calling `distribute()` function.

Moreover, the contract has the functionality to get the `votingPower` for a given tokenId` with the help of the functions `getVotingPowerAt()` (to get the `votingPower` at a certain timestamp). Also, there is a separate function called `_getLastVotingPowerSnapshotForVoter()` that just allows to get the latest snap for the voter.

The contract also contains some special admin functions such as `emergencyExecute()` and `abdicateHost()`.

The last thing is delegation functionality that's one of the main features of the contract and the crucial functions for implementing this logic are also inside of `PartyGovernance.sol`. `_adjustVotingPower()` updates the snap for the voter and his delegates and `_rebalanceDelegates()` rebalances the balance of the `oldDelegate` and `newDelegate` correspondingly.

`PartyGovernanceNFT.sol` - contains the functions that allow to mint, burn new tokenIds and update the `votingPower` for a given tokenId and `totalVotingPower` for the party. The `rageQuit()` mechanics is also implemented inside of this contract where the user can burn his tokenId himself and get the share of fungible tokens.

The protocol has a clear and consistent structure that makes the developing process and auditing process much easier. Regarding the specific details, there are a lot of proposal-related parameters (for example, proposal statuses) that become additional layer of complexity when it comes to extending the functionality. And because of the various restrictions that each parameter has, it becomes too difficult to add some new logic.

It's worth mentioning the docs are detailed too but there is always a place for improvement. Even though all the functions are thoroughly explained, it'd be better to have some sort of simple diagrams in the documents section so that the newly users of the system can get the clear understanding right away.


## Systemic Risks / Special areas of concern

Protocol has special areas of concern that have to be closely followed:

- The protocol has a lot of functions involving the transfer of ETH so it should be checked that the addresses
that the ETH is sent to can actually receive it.

- Delegation - the protocol actively uses delegation feature to delegate the voting power from the voter to somebody else:

`delegateVotingPower()` - the voter can transfer his voting power to another delegate
`_transferVotingPower()` - the voter can just transfer his own voting power
`mint()` - when minting new tokenId, the contributor has to specify who he wants to be a delegatee of his voting power

It should be checked that every user's snap is updated correctly and nothing is double counted
or, vice versa, not counted.

- Voting power calculation - when minting and burning new tokenIds, there are a lot of actions associated with
updating `totalVotingPower`, `mintedVotingPower`, `burnedVotingPower`. The developers should make sure that the values of these variables are displayed correctly as they have a big impact on the system - especially `totalVotingPower` as it's used in many places of the protocol. Two of my findings are connected with this problem and it can be seen how improper `totalVotingPower` calculation can influence the whole system. 

Moreover, it should be checked that the user himself gets his voting power calculated accurately when making contribution. 

- Proposal-related functionality - as said before, proposals have a huge number of params that are connected with them: `enum ProposalStatus`, `struct ProposalStateValues`, `struct ProposalState` and all of this is updated when calling one of the proposal-related functions.

- Proper access control system - it should be closely checked whether all the roles are used in the system correctly and there is no any place where access control system can be bypassed.


## Centralization Risks

The protocol has a quite huge percent of centralization and there are many functions that can only be called by admins and are influenced by them:

- For example, the authorities may call `increaseTotalVotingPower()` and depreciate somebody's share of a token in the case of him calling the `rageQuit()` function and he'll get the significantly less amount of tokens.

- There also some sensitive functions such as `emergencyExecute()` that idealistically should not be implemented in the system.

## Testability

Party Protocol's tests are on a very high level and are very easy to follow. Almost all the essential scenarios for the main protocol's functionality have been tested but there is always a place for some special edge cases (especially after the audit). 


## Conclusion

The protocol leaves a very good impression on me keeping in mind the fact that the code provided for the audit hasn't been audited before. It was really difficult to spot a bug (as there were noting superficial) and I had to really deep dive into functionality and consider a lot of complex scenarios to find anything. 

During the audit, I spotted potentially 1 high-severity bug and 4 medium-severity ones.

### Time spent:
30 hours