## [G-01] Nested loop to calculate ```withdrawAmounts``` in ```rageQuit```.
Nested loops are used to calculate ```withdrawAmounts``` in ```rageQuit```, which is inefficient. It can be optimized by:
1. Move the inner "tokenIds" loop out, and sum up the ```totalVotingPower``` of all ```tokenIds```, as it is independent from the ```withdrawTokens```.
2. Calculate the ```withdrawAmounts``` with previous ```totalVotingPower``` in the "withdrawTokens" loop.
```solidity
origin:
374:        // Sum up total amount of each token to withdraw.
375:        uint256[] memory withdrawAmounts = new uint256[](withdrawTokens.length);
376:        {
377:            IERC20 prevToken;
378:            for (uint256 i; i < withdrawTokens.length; ++i) {
                    ...
390:                // Add fair share of tokens from the party to total.
391:                for (uint256 j; j < tokenIds.length; ++j) {
392:                    // Must be retrieved before burning the token.
393:                    withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
394:                }
395:            }
396:        }

optimized:
            // Sum up total amount of each token to withdraw.
            uint256[] memory withdrawAmounts = new uint256[](withdrawTokens.length);
            {
                uint256 totalVotingPower;
                for (uint256 j; j < tokenIds.length; ++j) {
                    // Must be retrieved before burning the token.
                    totalVotingPower += getVotingPowerShareOf(tokenIds[j]);
                }
    
                IERC20 prevToken;
                for (uint256 i; i < withdrawTokens.length; ++i) {
                    // Add fair share of tokens from the party to total.
                    withdrawAmounts[i] = (balance * totalVotingPower) / 1e18;
                }
            }
```
[[L374-L396]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L374-L396)

