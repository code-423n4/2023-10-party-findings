- In InitialETHCrowdfund.batchContributeFor, it would be better to check the `msg.value == valuesSum` at the starting to throw at the start only without executing all the contributions.

- In PartyGovernance contact, `_assertHost`, `_assertActiveMember` might be changed to modifiers.

- In PartyGovernance._initialize, there are no checks for the `GovernanceValues` values, which are there in SetGovernanceParameterProposal contract.

- In PartyGovernance contract, the `_isUnanimousVotes`, `_areVotesPassing` function can avoid precision issue with division by avoid the division. Instead 
`_isUnanimousVotes` function can use this check `totalVotes * 1e4 >= 9999 * totalVotingPower`
`_areVotesPassing` function can use this check `voteCount * 1e4 >= totalVotingPower * passThresholdBps`

- Unuse constant (PROPOSAL_FLAG_UNANIMOUS) in `ProposalStorage` contract. Also reducing the redudancy, since that constant was already present in `LibProposal` contract.