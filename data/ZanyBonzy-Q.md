1. Improve the getCrowdFundLifecylce function. minContribution and minTotalContributions can be set to 0. Automatically marking the crowdfund as won, even if no single contribution has been made.
```
        if (block.timestamp >= expiry_) {
            if (totalContributions >= minTotalContributions) { //@note no contributions can be min
                return CrowdfundLifecycle.Won;
            } else {
                return CrowdfundLifecycle.Lost;
            }
        }
```
The creators can then wait till crowdfund expiry is reached, call the finalize function.
```
    function finalize() external {
        uint96 totalContributions_ = totalContributions; // 0

        // Check that the crowdfund is not already finalized.
        CrowdfundLifecycle lc = getCrowdfundLifecycle(); // won
        ...
           else {
            // Otherwise only allow finalization if the crowdfund has expired
            // and been won. Can be finalized by anyone.
            if (lc != CrowdfundLifecycle.Won) {
                revert WrongLifecycleError(lc);
            }
        }

        // Finalize the crowdfund.
        _finalize(totalContributions_);
    }
```
and mark the crowdfund as finalized. 

```
    function _finalize(uint96 totalContributions_) internal {
        // Finalize the crowdfund.
        delete expiry;
        ...
        emit Finalized();
    }
```
Basically having the party created without any contribtions, albeit votingPower is 0. 

2. The first [contribute](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L164C1-L176C6) function is not needed, it is exactly the same as the [second](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L185). A comment can be added informing users that using 0 as tokenId mints them a new party card
   
3. Consider limiting the [cancel](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L771) function to only hosts. It can be abused by users who are not in support of the proposal.
For instance, in a party of a hundred members, 98 supports the proposal, 2 don't. The proposal is passed, executed and now in progress. Any of the 2 members can call the function and cancel the proposal which goes against the majority decision.
```
 function cancel(uint256 proposalId, Proposal calldata proposal) external {
        _assertActiveMember(); //@note not interested?
        ...
``` 

4. Party can be left hostless if all available hosts abdicate. Also consider using two step (abdicate and accept) in the case that a party has one host, and the host status is transferred to the wrong address. These causes the host functions to be inaccessible.
```
    function abdicateHost(address newPartyHost) external {
        _assertHost();
        // 0 is a special case burn address.
        if (newPartyHost != address(0)) {
            // Cannot transfer host status to an existing host.
            if (isHost[newPartyHost]) {
                revert InvalidNewHostError();
            }
            isHost[newPartyHost] = true;
        } else {
            // Burned the host status
            --numHosts;
        }
        isHost[msg.sender] = false;
        emit HostStatusTransferred(msg.sender, newPartyHost);
    }
``` 
5. Authorities can also be relinquished which can leave authority functions inaccessible. Consider limiting this to only added authorities.
```
    function abdicateAuthority() external {
        _assertAuthority();
        delete isAuthority[msg.sender];

        emit AuthorityRemoved(msg.sender);
    }
```
6. Precision may be lost when converting contributed amounts to voting power and vice versa. It can lead to users being refunded less than they contributed.

When a user contributes, they're given a votingPower which is calculated using the formula.
`votingPower = (amount * exchangeRateBps) / 1e4;`
After the crowdfund is lost, users have the options to get refunded their contributions.
By doing this, their votingPower is reconverted back to contributions using the formula
`amount = (votingPower * 1e4) / exchangeRateBps;`

Noting that solidity rounds down, the votingPower calclated from amount contribted will be slightly less than actual votingPower, which will then be used to calculate the amount to refund.

For example, A user contributes 50 eth, to a party with an exchangeRateBps of 9900 (99%). Barring no fees and funding split, 
he recieves `(50 * 9900)/10000 = 49` votingPower, not 49,5 because solidity. 
When such user decides he wants a refund after crowdfund is lost. His contributed amount,
is `49 * 10000 / 9900 = 49` eth, not the 50 he initially contributed. 

He gets refunded 49 eth and loses 1 eth to the contract.  

7. 
```
        // Check that the contribution amount is at or above the minimum. This
        // is done after `amount` is potentially reduced if refunding excess
        // contribution. There is a case where this prevents a crowdfunds from
        // reaching `maxTotalContributions` if the `minContribution` is greater
        // than the difference between `maxTotalContributions` and the current
        // `totalContributions`. In this scenario users will have to wait until
        // the crowdfund expires or a host finalizes after
        // `minTotalContribution` has been reached by calling `finalize()`.
        uint96 minContribution_ = minContribution;
        if (amount < minContribution_) {
            revert BelowMinimumContributionsError(amount, minContribution_);
        }

```
In a case where a definite goal is set i.e minTotalContributions = maxTotalContributions. I.e the party wants the immediately finalize as soon as needed contributions are met. The required goal will be unreachable, as amount left after refunding excess(i.e the amount needed to bring the crowdfund to full equilibrum) will be less than the mincontribution needed. 

8. Users who have their their token balance i.e (the amount they're to withdraw) equal to minWithdrawAmount might not be able to rageQuit if there are fees involved.
User wants to quit the party and calls the rageQuit function
```
    function rageQuit(
        uint256[] calldata tokenIds,
        IERC20[] calldata withdrawTokens,
        uint256[] calldata minWithdrawAmounts,
        address receiver
    ) external {
```
The required checks are done and states are updated. Then fee is deducted
```
       ...
        {
            uint16 feeBps_ = feeBps;
        ...
                // Take fee from amount.
                uint256 fee = (amount * feeBps_) / 1e4;

                if (fee > 0) {
                    amount -= fee;
        ...
                }
```
As fee is the deducted, the new amount is then compared to the minimum wihtdrawal amount
```
                if (amount > 0) {
                    uint256 minAmount = minWithdrawAmounts[i];

                    // Check amount is at least minimum.
                    if (amount < minAmount) {
                        revert BelowMinWithdrawAmountError(amount, minAmount);
                    }
        ...
        emit RageQuit(msg.sender, tokenIds, withdrawTokens, receiver);
    }
```
which the user no longer meets, and therfore unable to leave. 
Recommend switching the positions, checking for the minAmount first, before deducting fees. 

9. [`// IDs of cards to credit the contributions to. When set to 0, it means`](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L65C33-L66C80)
    Incomplete comment.
10. Consider bounding array length at [L211](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L211) InitialEthCrowdfund
    `for (uint256 i; i < numContributions; ++i) {`
    and [L260](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L260C1-L260C59)
    `for (uint256 i; i < args.recipients.length; ++i) {`

