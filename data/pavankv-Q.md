# 1. First Priority for Zero check  ;-
In below scenario we can add zero check first rather than other operation check .

**Before**
```solidity
        if (opts.minTotalContributions > opts.maxTotalContributions) {
            revert MinGreaterThanMaxError(opts.minTotalContributions, opts.maxTotalContributions);
        }
        minTotalContributions = opts.minTotalContributions;
        // Set the max total contributions.
        if (opts.maxTotalContributions == 0) {
            // Prevent this because when `maxTotalContributions` is 0 the
            // crowdfund is invalid in `getCrowdfundLifecycle()` meaning it has
            // never been initialized.
            revert MaxTotalContributionsCannotBeZeroError(opts.maxTotalContributions);
        }

```

**After**
```solidity
      
        // Set the max total contributions.
        if (opts.maxTotalContributions == 0) { //@audit changed here
            // Prevent this because when `maxTotalContributions` is 0 the
            // crowdfund is invalid in `getCrowdfundLifecycle()` meaning it has
            // never been initialized.
            revert MaxTotalContributionsCannotBeZeroError(opts.maxTotalContributions);
        }
        
         if (opts.minTotalContributions > opts.maxTotalContributions) {
            revert MinGreaterThanMaxError(opts.minTotalContributions, opts.maxTotalContributions);
        }
        minTotalContributions = opts.minTotalContributions;

```
code snippet:-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L148C9-L158C10


## 2. Add a thershold for `fundingSplitBps` while intializing ;-

In below scenario we can see the comment [`basis points (e.g. 100 = 1%).`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L122) But there is no check while initializing the variable `fundingSplitBps`.

**Before**
```solidity
        // Set the funding split and its recipient.
        fundingSplitBps = opts.fundingSplitBps;
```

**After**
```solidity
       require(fundingSplitBps <= 1e4 && fundingSplitBps != 0 ,"Error");
        // Set the funding split and its recipient.
        fundingSplitBps = opts.fundingSplitBps;
```

code snippet:-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L167C1-L169C60

## 3. Add check before underflow error.
We can add the check whether the msg.value is equivalent to arguments values[]. As we know the arithmetic over/underflow doesn't return the gas if error occurs. We conducted the test by assinging the msg.value lesser than arguments values[] the result can shown below.

Result :-
```
Failing tests:
Encountered 1 failing test in test/crowdfund/InitialETHCrowdfund.t.sol:InitialETHCrowdfundTest
[FAIL. Reason: Arithmetic over/underflow] test_batchContribution_LargeargsValue() (gas: 1017826)
```
**Before**
```solidity
        // Check that the user has sent enough ETH.
        require(msg.value >= args.values.sum(), "Not enough ETH sent."); //@audit changed here.
        uint256 ethAvailable = msg.value;
        for (uint256 i; i < numContributions; ++i) {
            ethAvailable -= args.values[i];

```

**After**
```solidity
	
        uint256 ethAvailable = msg.value;
        for (uint256 i; i < numContributions; ++i) {
            ethAvailable -= args.values[i];


```

code snippet:-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L208C1-L212C44

## 4 . Zero check is not required in `_rebalanceDelegates()` function .
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

## 5. Utilize the short circuit condition and can be removed the redundant check 

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

| Feature  | Before  | After  |
|---|---|---|
| Redundancy   | Yes  | No  |
| Conciseness  | Less concise	  | More concise
  |
| Error-proneness	  | More error-prone	  | Less error-prone
  |
| Readability  | Easier to understand	   | Requires understanding of short-circuiting
  |
| Overall  | Less desirable  | More desirable
  |

code snippet:-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L724C1-L727C46

## 6. Remove redundant check to make code concise :-

In below scenario if statement checks whether the proposal is in `Inprogress` or not . Again same check is presented in `cancelProposal()` function in ProposalExecutionEngine.sol which can called by only through the delegate calls by `cancel()` function of PartyGovernance contract . Therefore can be remove the one of that check.

Check in `cancel()` function of PartyGovernance contract

```solidity
// Must be `InProgress`.
            ProposalStatus status = _getProposalStatus(values);
            if (status != ProposalStatus.InProgress) {
                revert BadProposalStatusError(status);
            }
```

Check in `cancelProposal()` function in ProposalExecutionEngine.sol 

```solidity
// Must be the current InProgress proposal.
            uint256 currentInProgressProposalId = stor.currentInProgressProposalId;
            if (currentInProgressProposalId != proposalId) {
                revert ProposalNotInProgressError(proposalId);
            }

```
It's better to remove the check in the cancel() function of the PartyGovernance contract since the cancelProposal() function in ProposalExecutionEngine.sol can be called by delegating, while the cancel() function can be called directly by active members and then delegated to cancelProposal().

code snippet:-
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L214C13-L218C14
