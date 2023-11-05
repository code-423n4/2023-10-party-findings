# May never be passed by all hosts
When the prompt is created, if a `host` calls `abdicateHost`, it will cause `PROPOSAL_FLAG_HOSTS_ACCEPT` to never be set. Even if all `host` are now `accept`.



The function `abdicateHost` will call `--numHosts` when the value of `newPartyHost` is 0, and `numHosts` will be reduced at this time.

```solidity
    /// @notice Transfer party host status to another.
    /// @param newPartyHost The address of the new host.
    function abdicateHost(address newPartyHost) external {
        _assertHost();
        // 0 is a special case burn address.
        if (newPartyHost != address(0)) {
            ...
        } else {
            // Burned the host status
            --numHosts;
        }
        isHost[msg.sender] = false;
        emit HostStatusTransferred(msg.sender, newPartyHost);
    }
```

When the proposal was created, numsHosts used the old numHosts.

```solidity
    function propose(
        Proposal memory proposal,
        uint256 latestSnapIndex
    ) external returns (uint256 proposalId) {
        ...
        (
            _proposalStateByProposalId[proposalId].values,
            _proposalStateByProposalId[proposalId].hash
        ) = (
            ProposalStateValues({
                proposedTime: uint40(block.timestamp),
                passedTime: 0,
                executedTime: 0,
                completedTime: 0,
                votes: 0,
                totalVotingPower: _getSharedProposalStorage().governanceValues.totalVotingPower,
                numHosts: numHosts,
                numHostsAccepted: 0
            }),
            getProposalHash(proposal)
        );
        ...
    }
```

This will ultimately affect the results of the two functions

```solidity
    function _hostsAccepted(
        uint8 snapshotNumHosts,
        uint8 numHostsAccepted
    ) private pure returns (bool) { 
        return snapshotNumHosts > 0 && snapshotNumHosts == numHostsAccepted;
    }
    
    function _getProposalFlags(ProposalStateValues memory pv) private pure returns (uint256) {
        uint256 flags = 0;
        if (_isUnanimousVotes(pv.votes, pv.totalVotingPower)) {
            flags = flags | LibProposal.PROPOSAL_FLAG_UNANIMOUS;
        }
        if (_hostsAccepted(pv.numHosts, pv.numHostsAccepted)) {
            flags = flags | LibProposal.PROPOSAL_FLAG_HOSTS_ACCEPT;
        }
        return flags;
    }
```

