### Summary

* \[L-01\] Wrong amounts may be emitted in the `ETHCrowdfundBase::_processContribution()`
    
* \[L-02\] Pre-existing ETH may not be credited to the `initialContributor`
    
* \[NC-01\] The protocol does not allow contributing in the last second
    
* \[NC-02\] Developer comments about the `BatchContributeArgs` struct in `InitialETHCrowdfund.sol` file is not complete.
    
* \[NC-03\] NatSpec explanation in the `PartyGovernance.sol::distribute()` is misleading
    
* \[NC-04\] Hosts can still veto after the veto period is passed.
    
* \[NC-05\] Users are not allowed to vote at the last second
    

---

### \[L-01\] Wrong amounts may be emitted in the `ETHCrowdfundBase::_processContribution()`

[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L210C51-L210C57](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L210C51-L210C57)  
[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L240](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L240)

This protocol allows users to contribute to crowdfunding. If the user's contribution causes the total contribution amount to exceed the maximum total contribution amount, excess funds are refunded to the user.

However, the `Contributed` event in this `_processContribution()` function is emitted with the user's input amount, not the actual contributed amount.

```solidity
    function _processContribution(
        address payable contributor,
        address delegate,
        uint96 amount
    ) internal returns (uint96 votingPower) {
        // skipped for brevity

210.-->   emit Contributed(msg.sender, contributor, amount, delegate); //@audit - This even is emitted with user input "amount"

        // skipped for brevity

            // Refund excess contribution.
            uint96 refundAmount = newTotalContributions - maxTotalContributions;
            if (refundAmount > 0) {
240.-->         amount -= refundAmount; //@audit - if the max total is reached, amount is decreased. There will be a mismatch between the event if this happens.
241.-->         payable(msg.sender).transferEth(refundAmount);
            }
        } else {
            totalContributions = newTotalContributions;
        }
```

If there is a refund due to `maxTotalContributions` is reached, there will be a mismatch between the `amount` in the emitted event and the actual contribution amount.

**Recommendation**: Emit the event after the contribution amount is certain.

---

### \[L-02\] Pre-existing ETH may not be credited to the `initialContributor`

[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L138C9-L151C10](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L138C9-L151C10)

```solidity
        // If the deployer passed in some ETH during deployment, credit them
        // for the initial contribution.
        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
        if (initialContribution > 0) {
            // If this contract has ETH, either passed in during deployment or
            // pre-existing, credit it to the `initialContributor`. //@audit QA - if this contract has pre-existing ETH but initialContribution (msg.value) is 0, these ETH will not be creditted at all because the function will not enter this if statement.
            _contribute(
                crowdfundOpts.initialContributor,
                crowdfundOpts.initialDelegate,
                initialContribution,
                0,
                ""
            );
        }
```

If the deployer passes some ETH during the deployment of the crowdfunding contracts, these ETH are contributed on behalf of `initialContributor`.

Above, we can see the developer's comments: "*If this contract has ETH, either passed in during deployment or pre-existing, credit it to the \`initialContributor\`"*

Unfortunately, this may not be true in some cases.

If the contract has pre-existing ETH, but the `msg.value` is 0 during the deployment, those pre-existing ETH will not be contributed on behalf of the `initialContributor` because the function will not enter the "if statement" at all.

**Recommendation:** I would recommend checking the balance of the contract and transferring those pre-existing ETH to the `initialContributor` even if the `msg.value` is 0.

---

### \[NC-01\] The protocol does not allow contributing in the last second

[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L185](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L185)

```solidity
          if (block.timestamp >= expiry_) { //@audit it doesn't allow contributing in the last second.
            if (totalContributions >= minTotalContributions) {
                return CrowdfundLifecycle.Won;
            } else {
                return CrowdfundLifecycle.Lost;
            }
```

As we can see above, crowdfunding status checks the expiry time to determine if the crowdfunding is lost or won. However, the check is wrong due to `>=` operator.

It doesn't allow contributions in the last second but these contributions made in the last second could have changed the status from `lost` to `won`.

**Recommendation**: use `block.timestamp > expiry_` instead of `block.timestamp >= expiry_`

---

### \[NC-02\] Developer comments about the `BatchContributeArgs` struct in `InitialETHCrowdfund.sol` file is not complete.

[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L66](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L66)

```solidity
    struct BatchContributeArgs {
        // IDs of cards to credit the contributions to. When set to 0, it means //@audit QA - the explanation is not complete. 
        uint256[] tokenIds;
```

Developer comments about the `BatchContributeArgs` struct is not complete.

---

### \[NC-03\] NatSpec explanation in the `PartyGovernance.sol::distribute()` is misleading

[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L474C48-L475C42](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L474C48-L475C42)

[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L526](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L526)

[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L534](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L534)

Part of the NatSpec explanation above the `PartyGovernance.sol::distribute()` function is: *"Create a token distribution by* ***moving the party's entire balance*** *to the* `TokenDistributor` *contract and immediately creating a distribution governed by this party."*

This comment is misleading because the function is not transferring the party's entire balance. It only transfers the inputted `amount` while creating the distribution.

```solidity
    /// @notice Create a token distribution by moving the party's entire balance
    ///         to the `TokenDistributor` contract and immediately creating a
    ///         distribution governed by this party.
    
    // skipped for brevity.
    function distribute(
        uint256 amount,
        ITokenDistributor.TokenType tokenType,
        address token,
        uint256 tokenId
    ) external returns (ITokenDistributor.DistributionInfo memory distInfo) {
        // skipped for brevity

        if (tokenType == ITokenDistributor.TokenType.Native) {
            return
-->             distributor.createNativeDistribution{ value: amount }( //@audit QA - amount is transferred not the entire balance
                    Party(payable(address(this))),
                    feeRecipient_,
                    feeBps_
                );
        }
        // Otherwise must be an ERC20 token distribution.
        assert(tokenType == ITokenDistributor.TokenType.Erc20);
-->     IERC20(token).compatTransfer(address(distributor), amount); //@audit QA - amount is transferred not the entire balance
        return
            distributor.createErc20Distribution(
                IERC20(token),
                Party(payable(address(this))),
                feeRecipient_,
                feeBps_
            );
    }
```

**Recommendation:** Consider updating the comment above the function.

---

### \[NC-04\] Hosts can still veto after the veto period is passed.

[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L677](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L677)

In the protocol's [contest page](https://code4rena.com/contests/2023-10-party-protocol#top), the execution delay for a proposal is used with the same meaning as the veto period. An example from the contest page: *"Add functionality to skip proposal’s veto period if all hosts have accepted it"*

In the `PartyGovernance.sol` contract `ProposalStatus` enum:

```solidity
    // States a proposal can be in.
    enum ProposalStatus {
        // skipped for brevity    

        // The proposal reached at least `passThresholdBps` of votes but is still
        // waiting for `executionDelay` to pass before it can be executed. Members
        // can continue to vote on the proposal and party hosts can veto at this time. //@audit QA - can they veto only this time?
        Passed,
        // Same as `Passed` but now `executionDelay` has been satisfied. Any member
        // may execute the proposal via `execute()`, unless `maxExecutableTime`
        // has arrived.
        Ready,
        
        // skipped for brevity
    }
```

It gives the idea that the hosts are only allowed to veto proposals during the `executionDelay` after the proposal is passed.

However, in the code itself, hosts can veto a proposal even if it is ready (*which means the veto period (a.k.a execution delay) has already passed.*)

```solidity
file: PartyGovernance.sol
//function veto()

            // Proposal must be in one of the following states.
            if (
                status != ProposalStatus.Voting &&
                status != ProposalStatus.Passed &&
-->             status != ProposalStatus.Ready //@audit QA - Isn't the veto period is between passed and ready. A ready veto should not be vetoed. 
            ) {
                revert BadProposalStatusError(status);
            }
```

**Recommendation:** It depends on the intention of the protocol. Either the hosts should not be allowed to veto after the proposal is ready, or the ability of the hosts should be explained in the docs/comments.

---

### \[NC-05\] Users are not allowed to vote at the last second

[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1104C1-L1107C10](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1104C1-L1107C10)

```solidity
        // Voting window expired.
        if (pv.proposedTime + gv.voteDuration <= t) { //@audit - it should be "<" instead of "<="
            return ProposalStatus.Defeated;
        }
```

As we can see above, the `voteDuration` of the proposal is added to `proposedTime`, but it doesn't allow voting in the last second of the voting period.

Even though it might be an extreme case, a vote at the last second could change the whole voting status from `Defeated` to `Passed`.

**Recommendation**: Include the last second of the voting to the voting period and use `<` instead of `<=`.