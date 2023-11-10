### [G&#x2011;1] Use assembly to emit events [Additional Instances]

To efficiently emit events, it's possible to utilize assembly by making use of scratch space and the free memory pointer. This approach has the advantage of potentially avoiding the costs associated with memory expansion.

However, it's important to note that in order to safely optimize this process, it is preferable to cache and restore the free memory pointer.

A good example of such practice can be seen in [Solady's](https://github.com/Vectorized/solady/blob/main/src/tokens/ERC1155.sol#L167) codebase.

<details>
<summary>There are 31 instances (click to show):</summary>

- *PartyGovernance.sol* ( [#L517](https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/party/PartyGovernance.sol#L517), [#L576](https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/party/PartyGovernance.sol#L576), [#L640](https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/party/PartyGovernance.sol#L640),[#L850](https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/party/PartyGovernance.sol#L850), [#L1022](https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/party/PartyGovernance.sol#L1022) ):

```solidity
517:         emit DistributionCreated(tokenType, token, tokenId);

576:         emit Proposed(proposalId, msg.sender, proposal);

640:         emit ProposalAccepted(proposalId, msg.sender, votingPower);

850:         emit EmergencyExecute(targetAddress, targetCallData, amountEth);

1022:        emit PartyVotingSnapshotCreated(
            voter,
            snap.timestamp,
            snap.delegatedVotingPower,
            snap.intrinsicVotingPower,
            snap.isDelegated
        );


```

- *ETHCrowdfundBase.sol* ( [#L383] (https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/crowdfund/ETHCrowdfundBase.sol#L383):

```solidity

383:         emit EmergencyExecute(targetAddress, targetCallData, amountEth);



```


- *PartyGovernanceNFT.sol* ([#L300] (https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/party/PartyGovernanceNFT.sol#L300),[#L447](https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/party/PartyGovernanceNFT.sol#L447)):

```solidity

300:         emit Burn(msg.sender, tokenId, votingPower);

447:     emit RageQuit(msg.sender, tokenIds, withdrawTokens, receiver);

```

### [G&#x2011;2] Efficiently Storing Booleans as `uint256`

#### Optimizing Gas Usage with `uint256` Instead of `Bools`

Using booleans (`bool`) for state variables in Solidity can unexpectedly increase gas consumption. Every write to a boolean involves an extra SLOAD operation to read the entire storage slot, modify the necessary bits, and then write it back. This extra step is required due to Solidity's safe contract upgrade protocols and pointer aliasing considerations. Alternatively, employing `uint256` with values `0` and `1` to signify false and true respectively can reduce gas usage by about 100 per operation by avoiding the additional SLOAD.

#### Code Examination
A prime example is found in the `ProposalEngineOpts` struct within the [ProposalStorage.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L27) of the 2023-10-party project. This struct contains multiple boolean fields:

```solidity
struct ProposalEngineOpts {
    bool enableAddAuthorityProposal;     // Gas-intensive
    bool allowArbCallsToSpendPartyEth;   // Gas-intensive
    bool allowOperators;                 // Gas-intensive
    bool distributionsRequireVote;       // Gas-intensive
}
```

#### Strategic Recommendations
1. **Transition to `uint256`**: For enhanced gas efficiency, replace `bool` with `uint256`, assigning `0` and `1` for false and true states, respectively. This method eliminates the unnecessary SLOAD operation, leading to significant gas savings.



### [G-3] Using Assembly to Check for Zero [Additional Instances]

When it comes to optimizing Solidity smart contracts for gas efficiency, one effective method is using assembly to check for zero values. This approach leverages the Ethereum Virtual Machine (EVM) more directly, reducing the overhead typically associated with higher-level Solidity operations.

**Code Snippet:**

In the context of checking for zero values, consider this example from the `OffChainSignatureValidator.sol`:

```solidity
1. if (signerVotingPowerBps == 0 && party.balanceOf(signer) == 0) {
2.     thresholdBps == 0 ||
```

This piece of code is pivotal in determining certain conditions based on zero values. By implementing an assembly-based approach, the gas costs associated with these operations could be reduced.

**GitHub References:**


- [Line 62](https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L62)
- [Line 72](https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L72)

- *ETHCrowdfundBase.sol* ( [#L202](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L202) ):

```solidity
202: if (msg.sender == contributor || oldDelegate == address(0)) {
```

This line checks if the `msg.sender` is the contributor or if the `oldDelegate` is set to the default address. It's a fundamental check for permissions or state validity in certain functions.

- *ETHCrowdfundBase.sol* ( [#L263](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L263) ):

```solidity
263: if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```

- *ETHCrowdfundBase.sol* ( [#L286](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L286) ):

```solidity
286: if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```

- *ETHCrowdfundBase.sol* ( [#L324](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L324) ):

```solidity
324: if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
```

Lines 263, 286, and 324 implement the same conditional check. They ensure that the `fundingSplitRecipient_` is not the zero address and that the `fundingSplitBps_` (basis points for funding split) is greater than zero. This is crucial for avoiding transactions to invalid addresses and ensuring that the funding split is meaningful.




### [G-4] Prefer `require` over `assert` for Efficient Error Handling

#### Description:
In Solidity, error handling and input validation are crucial for smart contract security and gas efficiency. Two primary keywords, `assert` and `require`, are used for these purposes, but their use-cases differ significantly.

#### Details:
- **`assert`:** This is used for conditions that should never be false. Failing an `assert` statement consumes all remaining gas and reverts all changes. It's generally used for verifying invariants or detecting internal errors.
- **`require`:** This keyword is employed for input validation or checking conditions that could arise during normal operation. Unlike `assert`, if `require` fails, it reverts all changes while refunding the remaining gas.

#### Best Practice:
- In scenarios where the condition (`currentInProgressProposalId == 0`) might be influenced by user inputs or external factors, `require` is preferable over `assert`. This is because `require` conserves gas in the event of a revert due to a failed condition check.

#### Example:
- In the following code snippet from the *ProposalExecutionEngine.sol* contract, `assert` is used, which could be less efficient in certain scenarios:
  ```solidity
  assert(currentInProgressProposalId == 0);
  ```
  - Source: [ProposalExecutionEngine.sol#L168](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L168)



### [G&#x2011;5] Remove Unused Reverts to Save Gas

In Solidity, unused `revert` statements can contribute to unnecessary gas usage. Removing these can optimize gas consumption, especially in frequently called functions.

The following instances in the `InitialETHCrowdfund` contract can be considered for removal to enhance gas efficiency:

- *InitialETHCrowdfund.sol*:
  - At [#L304](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L304), the line `revert ContributingForExistingCardDisabledError();` may be unnecessary if the condition triggering it is seldom true or can be handled differently.
  - Similarly, at [#L284](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L284), the `revert InvalidDelegateError();` could be redundant if the case it guards against is rare or can be checked in a more gas-efficient manner.

Removing or optimizing these lines can lead to reduced transaction costs and improved contract efficiency.


### [G&#x2011;6] Optimize Gas Usage by Removing Default Zero Value

In Solidity, initializing a variable with its default value (like zero for integers) is unnecessary and can lead to increased gas costs. An optimization technique is to avoid setting a variable to its default value when it's not needed.

The following snippet from the [InitialETHCrowdfund.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379) contract can be optimized:

- *InitialETHCrowdfund.sol* ([#L379](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379)):

```solidity
379:     for (uint i = 0; i < authoritiesLength - 1; ++i) {
```

In this line, the loop iterator `i` is initialized to `0`, which is the default value for `uint` in Solidity. To optimize for gas, this initialization can be omitted, as `uint` variables are `0` by default when declared. The optimized code would simply be:

```solidity
379:     for (uint i; i < authoritiesLength - 1; ++i) {
```

This minor change can lead to gas savings, especially in contracts that are deployed or interacted with frequently.



### [G&#x2011;7] Remove Unused Errors to Save Gas

Unused error declarations in a Solidity contract can lead to unnecessary gas consumption. It's advisable to review and remove any error declarations that are not being utilized in the contract. This practice not only optimizes gas usage but also enhances the readability and maintainability of the code.

In the [ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol) contract, the following unused error declarations have been identified:

- `NotAllowedByGateKeeperError` ([#L51](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L51))
- `NotOwnerError(uint256 tokenId)` ([#L60](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L60))
- `InvalidDelegateError()` ([#L62](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L62))
- `ContributingForExistingCardDisabledError()` ([#L69](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L69))
- `InvalidMessageValue()` ([#L73](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L73))

Removing these declarations can help in reducing the contract's deployment and execution costs.

