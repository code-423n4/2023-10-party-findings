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


## [G-02] Write back to storage after all fields updated.
Update ```passedTime``` to ```values``` (L651) first, and then write ```values``` back to ```info.values``` (move L639 before L657).
```solidity
File: contracts/party/PartyGovernance.sol
639:        info.values = values;
640:        emit ProposalAccepted(proposalId, msg.sender, votingPower);
641:
642:        // Update the proposal status if it has reached the pass threshold.
643:        if (
644:            values.passedTime == 0 &&
645:            _areVotesPassing(
646:                values.votes,
647:                values.totalVotingPower,
648:                _getSharedProposalStorage().governanceValues.passThresholdBps
649:            )
650:        ) {
651:            info.values.passedTime = uint40(block.timestamp);
652:            emit ProposalPassed(proposalId);
653:            // Notify third-party platforms that the governance NFT metadata has
654:            // updated for all tokens.
655:            emit BatchMetadataUpdate(0, type(uint256).max);
656:        }
657:        return values.votes;
```
[[L639-L657]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L639-L657)

## [G-03] Unnecessary "else".
"if" branch has return, there is no need for "else" (L408), just return.
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
384:        if (address(customMetadataProvider) == address(0)) {
385:            return ...

408:        } else {
409:            return ...

434:        }
```
[[L384-L434]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L384-L434)