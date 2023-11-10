### Report 1:
Repeating Calculation that would always return the same percentage value in a loop would use excess gas. 
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L393
```solidity
 function rageQuit(
        ...
    ) external {
          ...
  {
            IERC20 prevToken;
            for (uint256 i; i < withdrawTokens.length; ++i) {
              ...
                  // Add fair share of tokens from the party to total.
                for (uint256 j; j < tokenIds.length; ++j) {
                    // Must be retrieved before burning the token.
>>>                    withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
                }
            }
    }
```
At every loop of "i" to  withdrawTokens.length, The value of calculation of overall percentage value of ... getVotingPowerShareOf(tokenIds[j])) / 1e18; would always be the same since it is the same token that is being looped through by "j" loop at every loop of "i". Sponsor also confirmed this, a more efficient way would be to perform this percentage calculation with "j" outside this way it would be done only once instead of repeating it at every loop of "i".
#### Fix
```solidity
 function rageQuit(
        ...
    ) external {
          ...
  {
            IERC20 prevToken;
+++        uint256 VotingPowerSharPercent;
+++                for (uint256 j; j < tokenIds.length; ++j) {
+++                    VotingPowerSharPercent +=  (1e8 * getVotingPowerShareOf(tokenIds[j])) / 1e18; //1e8 is used as precision, this can be any value preferred by sponsor
+++                }
            for (uint256 i; i < withdrawTokens.length; ++i) {
               ... 
                  // Add fair share of tokens from the party to total
+++               withdrawAmounts[i] = (balance * VotingPowerSharPercent) / 1e8; // 1e8 is used as precision as stated earlier
---                for (uint256 j; j < tokenIds.length; ++j) {
---                    // Must be retrieved before burning the token.
---                    withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
---                }
            }
    }
```
This way the "j" loop is done only once.
###  Report 2:
Memory should be used instead of storage for voterSnaps to save gas since no new data is to saved into _votingPowerSnapshotsByVoter[voter] at any point in the function implementation and since it is a view function
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1046
```solidity
   function _getLastVotingPowerSnapshotForVoter(
        address voter
    ) private view returns (VotingPowerSnapshot memory snap) {
        VotingPowerSnapshot[] storage voterSnaps = _votingPowerSnapshotsByVoter[voter];
        uint256 n = voterSnaps.length;
        if (n != 0) {
            snap = voterSnaps[n - 1];
        }
    }
```