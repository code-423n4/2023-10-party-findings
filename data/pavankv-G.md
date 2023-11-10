## 1 . Zero check is not required in `_rebalanceDelegates()` function .
In `_rebalanceDelegate()` function have the zero check of the `newDelegate` and `newDelegate` variable but it has been done in [`_adjustVotingPower()`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L944 ) function only.

Check in `_adjustVotingPower()`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L947C1-L951C68) function 
```solidity
        // If `oldDelegate` is zero and `voter` never delegated, then have
        // `voter` delegate to themself.
        oldDelegate = oldDelegate == address(0) ? voter : oldDelegate;
        // If the new `delegate` is zero, use the current (old) delegate.
        delegate = delegate == address(0) ? oldDelegate : delegate;
```

**Code in [`_rebalanceDelegates()`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L979C8-L981C10)**
```solidity
        if (newDelegate == address(0) || oldDelegate == address(0)) {
            revert InvalidDelegateError();
        }
```

code snippet :-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L979C1-L981C10


## 2. Utilize the short circuit condition and can be removed the redundant check to save gas.

In the following scenario, we can observe short-circuiting being used. If the first condition, status equals ProposalStatus.Ready, then the second condition will not be evaluated. This is because for the error to be reverted, both conditions must be not equal to the expression. Following this, there is a check to see if status equals ProposalStatus.Ready. So, if the execution passes the first if statement, the second if statement will always be true, making it redundant.Have a look over **After** section.

**Before**
```solidity
        if (status != ProposalStatus.Ready && status != ProposalStatus.InProgress) {
            revert BadProposalStatusError(status);
        }
        if (status == ProposalStatus.Ready) {
```

**After**
```solidity
        if (status != ProposalStatus.InProgress &&status != ProposalStatus.Ready) { //@audit changed here.
            revert BadProposalStatusError(status);
        }
        if (status == ProposalStatus.Ready) {
```

The "After" version is more gas-optimized. This is because it eliminates the redundant check for status == ProposalStatus.Ready after the first if statement. This redundant check consumes gas, as it requires the EVM to perform an unnecessary comparison. The "After" version avoids this unnecessary comparison, which saves gas.By eliminating the redundant check for status == ProposalStatus.Ready, the "After" version reduces the gas cost of the code. This makes the code more efficient and cost-effective.

code snippet:-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L724C1-L727C46

## 3. Remove redundant check to make code concise and to save gas :-

In below scenario if statement checks whether the proposal is in `Inprogress` or not . Again same check is presented in `cancelProposal()` function in ProposalExecutionEngine.sol which can called by only through the delegate calls by `cancel()` function of PartyGovernance contract . Therefore can be remove the one of that check.

Check in `cancel()` function of PartyGovernance contract

```solidity
// Must be `InProgress`.
            ProposalStatus status = _getProposalStatus(values);
            if (status != ProposalStatus.InProgress) {
                revert BadProposalStatusError(status);
            }
```

code snippet:-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L779C12-L784C10


Check in `cancelProposal()` function in ProposalExecutionEngine.sol 

```solidity
// Must be the current InProgress proposal.
            uint256 currentInProgressProposalId = stor.currentInProgressProposalId;
            if (currentInProgressProposalId != proposalId) {
                revert ProposalNotInProgressError(proposalId);
            }

```
code snippet :-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L214C13-L218C14

It's better to remove the check in the cancel() function of the PartyGovernance contract since the cancelProposal() function in ProposalExecutionEngine.sol can be called by delegating, while the cancel() function can be called directly by active members and then delegated to cancelProposal().

## 4. Do-while loop is more-efficient than for-loop

**Before**
```solidity
for (uint i = 0; i < authoritiesLength - 1; ++i) {
            authorities[i] = opts.authorities[i];
        }
```

**After**
```solidity
uint256 i;
do{
authorities[i] = opts.authorities[i];
unchecked{++i;}
}while(i < authoritiesLength - 1);
```

code snippet:-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379C9-L381C10

## 5. Refactor the short-circuit to save gas :-
Below scenario we can notice that first condition compares `fundingSplitRecipient_` to zero address , the zero unsigned interger converts to address and second condition compares `fundingSplitBps_` to zero there is no type cast here finally short-circuit uses `||` or operator if first condition it never check the second condition . ThereFore here second conditon is cheaper than first condition .

**Before**
```solidity
if (fundingSplitRecipient_ == address(0) || fundingSplitBps_ == 0) {
            revert FundingSplitNotConfiguredError();
        }
```

**After**
```solidity
 if ( fundingSplitBps_ == 0 ||fundingSplitRecipient_ == address(0)) { // @audit changed
            revert FundingSplitNotConfiguredError();
        } 
```

Difference can seen Below :-

```
Before if statement:
- If fundingSplitRecipient_ is zero:
- Cost of checking fundingSplitRecipient_ is zero: 3 gas
- Cost of reverting: 15 gas
- If fundingSplitBps_ is zero:
- Cost of checking fundingSplitBps_ is zero: 3 gas
- Cost of reverting: 15 gas
- Total: 36 gas

After if statement:
- If fundingSplitBps_ is zero or fundingSplitRecipient_ is zero:
- Cost of checking both fundingSplitBps_ and fundingSplitRecipient_ at the same time: 6 gas
- Cost of reverting: 15 gas
- Total: 21 gas

As you can see, the After if statement is 15 gas cheaper than the Before if statement.
```

code snippet:-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L348C1-L350C10