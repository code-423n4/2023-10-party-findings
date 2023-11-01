**Gas Optimization Recommendation**

**Description:**

In the findVotingPowerSnapshotIndex function of the contract (ln 423 in PartyGovernance.sol, I have identified an opportunity to optimize gas usage when a specific condition is met. The current implementation includes a binary search algorithm to find the index of the most recent voting power snapshot, but in cases where no valid voting snapshots exist, gas can be conserved by returning type(uint256).max early.

Details:

Function: findVotingPowerSnapshotIndex
Location: https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/party/PartyGovernance.sol#L423C3-L423C3

Recommendation:
I recommend modifying the findVotingPowerSnapshotIndex function as follows to optimize gas usage:

```
function findVotingPowerSnapshotIndex(
    address voter,
    uint40 timestamp
) public view returns (uint256 index) {
    VotingPowerSnapshot[] storage snaps = _votingPowerSnapshotsByVoter[voter];
    uint256 high = snaps.length;

    // Return `type(uint256).max` if no valid voting snapshots found.
    if (high == 0) {
        return type(uint256).max;
    }

    uint256 low = 0;
    while (low < high) {
        uint256 mid = (low + high) / 2;
        if (snaps[mid].timestamp > timestamp) {
            high = mid;
        } else {
            low = mid + 1;
        }
    }
    return high - 1;
}
```


This change will result in gas savings when no valid voting snapshots are present, without affecting the contract's intended functionality.

Severity: Low/Non-Critical

Impact: This optimization improves gas efficiency without introducing security risks or affecting the contract's core functionality.


