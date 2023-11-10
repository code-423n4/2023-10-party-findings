1.

Decreasing totalVotingPower should not make it less than minted voting power.

Add

``` solidity
totalVotingPower >= mintedVotingPower
```
to

```solidity
function decreaseTotalVotingPower(uint96 votingPower) external {
    _assertAuthority();
    _getSharedProposalStorage().governanceValues.totalVotingPower -= votingPower;
}
```

If totalVotingPower was decreased below the amount already minted, it would cause issues:

- New NFTs could no longer be minted, since there is insufficient total voting power.
- Existing NFT percentages would be wrong, since the total is less than the amount minted.
- Could potentially break calculations that use totalVotingPower as a denominator.

2.

In the rageQuit function, the array of tokens from which a user can withdraw should contain at most one instance of the contract's Ether balance. This is due to the fact that address(this).balance is used to calculate the withdrawal amount, and after the first withdrawal of Ether, the balance will be reduced, potentially to zero, affecting any subsequent Ether withdrawal calculations within the same transaction.

Suggest the following code changes

```diff
function rageQuit(
    uint256[] calldata tokenIds,
    IERC20[] calldata withdrawTokens,
    uint256[] calldata minWithdrawAmounts,
    address receiver
) external {
    if (tokenIds.length == 0) revert NothingToBurnError();

    // Check if called by an authority.
    bool isAuthority_ = isAuthority[msg.sender];

    // Check if ragequit is allowed.
    uint40 currentRageQuitTimestamp = rageQuitTimestamp;
    if (!isAuthority_) {
        if (currentRageQuitTimestamp != ENABLE_RAGEQUIT_PERMANENTLY) {
            if (
                currentRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY ||
                currentRageQuitTimestamp < block.timestamp
            ) {
                revert CannotRageQuitError(currentRageQuitTimestamp);
            }
        }
    }

    // Used as a reentrancy guard. Will be updated back after ragequit.
    rageQuitTimestamp = DISABLE_RAGEQUIT_PERMANENTLY;

+   // Initialize a flag to track if ETH has been processed
+   bool isEthWithdrawn = false;

    // Sum up total amount of each token to withdraw.
    uint256[] memory withdrawAmounts = new uint256[](withdrawTokens.length);
    {
+       for (uint256 i; i < withdrawTokens.length; ++i) {
+           IERC20 token = withdrawTokens[i];

+           // Check if the current token is the special ETH_ADDRESS
+           if (address(token) == ETH_ADDRESS) {
+               // If ETH has already been processed, revert the transaction
+               if (isEthWithdrawn) {
+                   revert("Only one entry for ETH withdrawal is allowed.");
+               }
+               // Set the flag to true as ETH has been processed
+               isEthWithdrawn = true;
+           }

+           // Check token's balance.
+           uint256 balance;
+           if (address(token) == ETH_ADDRESS) {
+               balance = address(this).balance;
+           } else {
+               balance = token.balanceOf(address(this));
+           }

+           // Calculate the withdrawable amount based on the voting power shares
+           for (uint256 j; j < tokenIds.length; ++j) {
+               withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
+           }
+       }
    }

    // Burn caller's party cards and adjust voting power
    // ... (presumed existing logic for burning NFTs and adjusting voting power)

    // Transfer the withdrawable amounts to the receiver
    // ... (presumed existing logic for transferring amounts to the receiver)

    // Reset ragequit timestamp to its original state
    rageQuitTimestamp = currentRageQuitTimestamp;

    // Emit an event for the ragequit
    // ... (presumed existing logic for emitting RageQuit event)
}

```

3.

As ETHCrowdfundBase#getCrowdfundLifecycle checks 

```solidity
if (maxTotalContributions == 0) {
  return CrowdfundLifecycle.Invalid;
},
```

we should also do the same validation check on minTotalContribution,

suggest code changes

```diff
function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {
+       if (minTotalContributions == 0) {
+         return CrowdfundLifecycle.Invalid;
+       }

        if (maxTotalContributions == 0) {
            return CrowdfundLifecycle.Invalid;
        }

        uint256 expiry_ = expiry;
        if (expiry_ == 0) {
            return CrowdfundLifecycle.Finalized;
        }

        if (block.timestamp >= expiry_) {
            if (totalContributions >= minTotalContributions) {
                return CrowdfundLifecycle.Won;
            } else {
                return CrowdfundLifecycle.Lost;
            }
        }

        return CrowdfundLifecycle.Active;
    }
```

4.

Vote should not be 0. Right now, PartyGovernance#accept can accept 0 vote

Suggestion, change accept function

```diff
function accept(uint256 proposalId, uint256 snapIndex) public returns (uint256 totalVotes) {
        // Get the information about the proposal.
        ProposalState storage info = _proposalStateByProposalId[proposalId];
        ProposalStateValues memory values = info.values;

        // Can only vote in certain proposal statuses.
        {
            ProposalStatus status = _getProposalStatus(values);
            // Allow voting even if the proposal is passed/ready so it can
            // potentially reach 100% consensus, which unlocks special
            // behaviors for certain proposal types.
            if (
                status != ProposalStatus.Voting &&
                status != ProposalStatus.Passed &&
                status != ProposalStatus.Ready
            ) {
                revert BadProposalStatusError(status);
            }
        }

        // Prevent voting in the same block as the last rage quit timestamp.
        // This is to prevent an exploit where a member can rage quit to reduce
        // the total voting power of the party, then propose and vote in the
        // same block since `getVotingPowerAt()` uses `values.proposedTime - 1`.
        // This would allow them to use the voting power snapshot just before
        // their card was burned to vote, potentially passing a proposal that
        // would have otherwise not passed.
        if (lastRageQuitTimestamp == block.timestamp) {
            revert CannotRageQuitAndAcceptError();
        }

        // Cannot vote twice.
        if (info.hasVoted[msg.sender]) {
            revert AlreadyVotedError(msg.sender);
        }
        // Mark the caller as having voted.
        info.hasVoted[msg.sender] = true;

        // Increase the total votes that have been cast on this proposal.
        uint96 votingPower = getVotingPowerAt(msg.sender, values.proposedTime - 1, snapIndex);
+.      require(votingPower > 0, "Voting power cannot be 0");

        values.votes += votingPower;
        if (isHost[msg.sender]) {
            ++values.numHostsAccepted;
        }
        info.values = values;
        emit ProposalAccepted(proposalId, msg.sender, votingPower);

        // Update the proposal status if it has reached the pass threshold.
        if (
            values.passedTime == 0 &&
            _areVotesPassing(
                values.votes,
                values.totalVotingPower,
                _getSharedProposalStorage().governanceValues.passThresholdBps
            )
        ) {
            info.values.passedTime = uint40(block.timestamp);
            emit ProposalPassed(proposalId);
            // Notify third-party platforms that the governance NFT metadata has
            // updated for all tokens.
            emit BatchMetadataUpdate(0, type(uint256).max);
        }
        return values.votes;
    }
```
