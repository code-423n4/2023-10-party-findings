# QA

## Remove return statement with empty string - `SetGovernanceParameterProposal::_executeSetGovernanceParameter`

The `SetGovernanceParameterProposal::_executeSetGovernanceParameter()` function just returns an empty string after the function execution. This is unnecessary and confusing. Removing it will also provide some gas savings.

Code Link: https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol#L65

```diff
        function _executeSetGovernanceParameter(
            IProposalExecutionEngine.ExecuteProposalParams memory params
        ) internal returns (bytes memory) {
            ...
            ...
65:         return "";
        }
```

### Recommendation

Remove the return statement

```diff
        function _executeSetGovernanceParameter(
            IProposalExecutionEngine.ExecuteProposalParams memory params
        ) internal returns (bytes memory) {
            ...
            ...
-           return "";
        }
```

## In `PartyGovernanceNFT::_getProposalStatus()`, the comparison `pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT` can be misleading to the reader

In `PartyGovernanceNFT::_getProposalStatus()` method, there is a condition which checks if the high bit is set, by doing `pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT`. 

Code Link: https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1077-L1079

```
1077:    if (pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT) {
1078:        return ProposalStatus.Cancelled;
1079:    }
```

This can be misleading to the reader, and can instead be interpreted as:

```
-> pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT
-> (pv.completedTime) & (UINT40_HIGH_BIT == UINT40_HIGH_BIT)
-> pv.completedTime & true
```

instead of the following which is the actual intention

```
-> pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT
-> (pv.completedTime & UINT40_HIGH_BIT) == UINT40_HIGH_BIT
```

### Recommendation
Wrapping the `&` operator in a simple parantheses will really amplify the readability of this bitwise operation, and the whole expression

```diff
- if (pv.completedTime & UINT40_HIGH_BIT == UINT40_HIGH_BIT) {
+ if ((pv.completedTime & UINT40_HIGH_BIT) == UINT40_HIGH_BIT) {
    return ProposalStatus.Cancelled;
}
```

# Low

## `PartyGovernanceNFT` has no methods to remove an authorized user without them explicitly initiating the process

The `PartyGovernanceNFT::addAuthority()` function allows adding of a new authority, and the `PartyGovernanceNFT::abdicateAuthority()` function allows giving up of the authority.

But there is no methods to remove authority. If a malicious authority is added, then they can perform several powerful actions such as minting cards and update voting power for the party.

Code Link: https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L476-L491

```diff
476:     /// @notice Add a new authority.
477:     /// @dev Used in `AddAuthorityProposal`. Only the party itself can add
478:     ///      authorities to prevent it from being used anywhere else.
479:     function addAuthority(address authority) external onlySelf {
480:         isAuthority[authority] = true;
481:
482:         emit AuthorityAdded(authority);
483:     }
484:
485:    /// @notice Relinquish the authority role.
486:    function abdicateAuthority() external {
487:        _assertAuthority();
488:        delete isAuthority[msg.sender];
489:
490:        emit AuthorityRemoved(msg.sender);
491:    }
```

### Recommendation

Add a method where a powerful user like the `party host` can remove such authorized users

```diff
+   function removeAuthority(address user) external {
+       if (isHost[msg.sender]) {
+           revert OnlyHostAllowerd();
+       }
+       delete isAuthority[user];
+   
+       emit AuthorityRemoved(user);
+   }
```

## Cannot re-enable emergency execute in `PartyGovernance` after its disabled

The `PartyGovernanceNFT::disableEmergencyExecute()` function can be used to disable emergency execution by the Party DAO or the host. But there are no corresponding methods to re-enable emergency execute.

Code Link: https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L853-L858

```diff
853:    /// @notice Revoke the DAO's ability to call emergencyExecute().
854:    /// @dev Either the DAO or the party host can call this.
855:    function disableEmergencyExecute() external onlyPartyDaoOrHost {
856:        emergencyExecuteDisabled = true;
857:        emit EmergencyExecuteDisabled();
858:    }
```

### Recommendation

Add a method which can be re-enable emergency execute.

```diff
+   function enableEmergencyExecute() external onlyPartyDaoOrHost {
+       emergencyExecuteDisabled = true;
+       emit EmergencyExecuteEnabled();
+   }
```
