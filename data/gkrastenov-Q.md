# [L-01] User can deacrease totalVotingPower of the party
When a token is burned, only `mintedVotingPower` is decreased by the voting power of the burned token. The authority has the right to change `totalVotingPower` through the calling of `increase/decreaseTotalVotingPower` functions.

Every user can manipulate `totalVotingPower` by decreasing it. They need to call the `rageQuit` function with `withdrawTokens.length == 0` and will burn their tokens. All tokens will be burned and the sum of the voting power of the tokens, `totalVotingPowerBurned`, will be subtracted from `totalVotingPower`.

```solidity
 _getSharedProposalStorage().governanceValues.totalVotingPower -= totalVotingPowerBurned; 
```

At some point, `totalVotingPower` may become less than `mintedVotingPower` and the minting of new tokens will be blocked. Additionally, it is expected that only the authority has the right to increase/decrease the `totalVotingPower` of the party.

## Recommendation: 
Do not decrease `totalVotingPower` after calling of `rageQuit` function.

# [L-02] TotalVotingPower can be lower than mintedVotingPower
When `totalVotingPower` is decreased by the authority, it is not checked whether `mintedVotingPower <= _getSharedProposalStorage().governanceValues.totalVotingPower -= votingPower`. When new tokens are minted or voting power for a specific token is increased, checks are always performed to ensure that `mintedVotingPower` will not exceed the `totalVotingPower`. However, this check is missed when `totalVotingPower` is directly decreased. This will block the minting of new tokens until the authority calls `increaseTotalVotingPower` to return to the old state.

## Recommendation: 
Add check for `mintedVotingPower <= _getSharedProposalStorage().governanceValues.totalVotingPower -= votingPower` in `decreaseTotalVotingPower` function.


# [L-03] Possible to increase votingPower of tokenId which is invalid
It is possible to increase the voting power of a tokenId, even when it is invalid. When a user calls the `contributeFor` function with `recipient == address(0)` and `tokenId > tokenCount`, the voting power will be assigned to a tokenId that has not been minted yet. This can easily be done because the check `(party.ownerOf(tokenId) == contributor)` will be true.

## Recommendation: 
Add check for `contributor address` to be different than `address(0)` in `_contribute` function.

# [NC-01] 
Once disableEmergencyExecute is set to true, it is not possible for the DAO or the host to disable emergency execute again. This may be problematic if they decide to enable it again.

```solidity
emergencyExecuteDisabled = true; 
emit EmergencyExecuteDisabled();
```

