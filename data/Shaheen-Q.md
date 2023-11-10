# Findings Summary:
| No | Issue |
| :-----: | :-----: |
| L-01 | Veto period will not be skipped even after all hosts' votes |
| L-02 | Veto period will not be skipped even after all hosts' votes if the new Host had already accepted the proposal |
| L-03 | Base Mainnet do not support 0.8.20, 0.8.19 recommended |
| L-04 | `decreaseTotalVotingPower()` can decease the `totalVotingPower` more than the already `mintedVotingPower` |
| L-05 | Important users' input authentications missing |

# Low Findings:

## L-01: Veto period will not be skipped even after all hosts' votes
The veto period skips when all the hosts accept the proposal. But there is some cases where it will not.

A party member initiates a proposal with an initial count of 10 hosts required for the veto period to be skipped. In order to skip the veto period, the `numHostsAccepted` must reach 10. However, if a host decides to burn their position by invoking the `abdicateHost()` function, the actual count of hosts (`numHosts`) in the party reduces to 9. So, even if all current 9 hosts accept the member's proposal, they will still need to wait for the veto period to elapse because the current count of hosts (`numHostsAccepted` = 9) does not match the required count of accepted hosts (`numHosts` = 10).

#### Mitigation:
1. Do not allow `abdicateHost()` during an ongoing proposal.
2. Decrease the `numHosts` for the on-going proposals if a host burns his position

## L-02: Veto period will not be skipped even after all hosts' votes if the new Host had already accepted the proposal
A party member initiates a proposal with an initial requirement of 10 hosts for the veto period to be bypassed. To achieve this, the `numHostsAccepted` must reach 10. However, during the voting process, if a host opts to transfer their position to a new host using the `abdicateHost(newHost)` function, and if the new host had already accepted the proposal before receiving the host position, they will be unable to vote again. Consequently, they will not be included in the `numHostsAccepted` count. As a result, even if every remaining host votes, the proposal will still fail to meet the criteria of `numHostsAccepted (9) == numHosts (10)`.

Again all hosts voted but the veto period not skipped.

#### Mitigation:
1. Do not allow `abdicateHost()` during an ongoing proposal xD
2. create a new function for newHosts to register themselves in the `numHostsAccepted` when they have already voted for the proposal.

Note: The `L-01` & `L-02` the edge-cases of the high-severity issue I have submitted and discussed in detail. For additional information, please refer to the high issue titled: _`_hostsAccepted()` can return true even without all hosts' votes, which can be exploited by a malicious host to execute any proposal immediately._

## L-03: Base Mainnet do not support 0.8.20, 0.8.19 recommended
The protocol contracts use `0.8.20` solidity pragma version which is not supported yet on bedrock chains as [Base Docs](https://docs.base.org/differences/) state: "Aside from the above, Base Mainnet and Testnet do not yet support the new PUSH0 opcode introduced in Shanghai, which is the default target for the Solidity compiler if you use version 0.8.20 or later.
We recommend using 0.8.19 or lower until this is upgraded"

#### Migitation
```diff
     // SPDX-License-Identifier: GPL-3.0
-    pragma solidity 0.8.20;
+    pragma solidity 0.8.19;
```

Request for the judge: In a previous C4 contest, this was accepted as a valid [medium](https://github.com/code-423n4/2023-05-ambire-findings/issues/12) finding, to me it doesn't look medium severity that's why I'm submitting it as a Low severity. But if this gets accepted as a medium again then pls judge sahab bump this to medium as well. Thanks!

## L-04: `decreaseTotalVotingPower()` can decease the `totalVotingPower` more than the already `mintedVotingPower`
`decreaseTotalVotingPower()` doesn't make sure that it doesn't decrease the `totalVotingPower` more than the already `mintedVotingPower`. Which can result in overflows/underflow errors exceptions for the users

Make sure to add this check:
```solidity
    function decreaseTotalVotingPower(uint96 votingPower) external {
       _assertAuthority();

       _getSharedProposalStorage().governanceValues.totalVotingPower -= votingPower;
       require(_getSharedProposalStorage().governanceValues.totalVotingPower < mintedVotingPower, "ERR")
    }
```

## L-05: Important users' input authentications missing
This issue contains all the important missing user inputs checks:

### 1. Equlity check for the length of `values` array & `tokenIds` array missing
The dev comment in `BatchContributeArgs` states that:
> // The length of this (values) array must be equal to the length of `tokenIds`.

But this check is missing in the `batchContribute()`
```solidity
function batchContribute(
        BatchContributeArgs calldata args
    ) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {
        ///@audit-issue args.values == args.tokenIds.length not checked!
        uint256 numContributions = args.tokenIds.length;
        votingPowers = new uint96[](numContributions);
        uint256 ethAvailable = msg.value;
        .....
}
```
Make sure to add it.

### 2. Zero check missing for `voteDuration` & `executionDelay`
voteDuration & executionDelay are important so a shark cannot propose, accept & execute directly. If it accidentaly gets set to zero it can cause lot of problems so consider adding Zero checks