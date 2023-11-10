# QA Report

## Table of Contents

| Issue ID                                                                                            | Description                                                                                    |
| --------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| [QA-01](#qa-01-there-are-no-assurances-that-emergencyexecute-would-ever-fulfill-its-purpose)        | There are no assurances that `emergencyExecute()` would ever fulfill its purpose               |
| [QA-02](#qa-02-misusal-of-_ragequit-could-heavily-impact-proposal-outcomes)                         | Misuse of `_rageQuit()` could heavily impact proposal outcomes                                 |
| [QA-03](#qa-03-remove-redundant-naming-convention-for-custom-errors)                                | Remove redundant naming convention for custom errors                                           |
| [QA-04](#qa-04-ragequits-execution-window-could-be-inaccurate)                                      | `rageQuit()`'s execution window could be inaccurate                                            |
| [QA-05](#qa-05-some-safety-measures-should-be-implemented-before-deployment-on-base)                | Some safety measures should be implemented before deployment on BASE                           |
| [QA-06](#qa-06-inconsistency-in-sister-function-implementations)                                    | Inconsistency in sister function implementations                                               |
| [QA-07](#qa-07-signingthersholdbps-should-be-signingthresholdbps-instead)                           | `signingThersholdBps` should be `signingThresholdBps` instead                                  |
| [QA-08](#qa-08-burn-should-include-a-similar-check-like-the-prevtoken-withdrawtokens-from-ragequit) | `burn` should include a similar check like the `prevToken >= withdrawTokens` from `rageQuit()` |
| [QA-09](#qa-09-self-delegation-mechanism-seems-to-be-flawed)                                        | Self-delegation mechanism seems to be flawed                                                   |
| [QA-10](#qa-10-fix-contextual-errors-in-docs)                                                       | Fix contextual errors in docs                                                                  |

## QA-01 There are no assurances that `emergencyExecute()` would ever fulfill it's purpose

### Impact

Low, since this relies on having a restricted host_.

But current implementation of the emergency execution mechanism indirectly have the _host_ to give the final say, which is unintended or atleast not in the implemented context. is to allow the DOA execute any arbitrary function from the contract, issue is host can preempt the DAO's decision to execute an emergency action. This is due to the ability of a host to disable the `emergencyExecute` function, potentially compromising the DAO's ability to respond in critical situations.

### Proof of Concept

Take a look at [emergencyExecute()](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L366)

- The `emergencyExecute` function is intended for the DAO to perform emergency actions.
  `solidity
function emergencyExecute(
  address targetAddress,
  bytes calldata targetCallData,
  uint256 amountEth
) external payable {
  // Must be called by the DAO.
  if (_GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET) != msg.sender) {
    revert OnlyPartyDaoError(msg.sender);
  }
  // Must not be disabled by DAO or host.
  if (emergencyExecuteDisabled) {
    revert OnlyWhenEmergencyActionsAllowedError();
  }
  //omitted for brevity
}
`
  Function is used to execute `arbitrary functions` for the DAOs, now issue is that the `disableEmergencyExecute()` also exists as can be seen below:
  `solidity
function disableEmergencyExecute() external {
  // Only the DAO or a host can call this.
  if (
    !party.isHost(msg.sender) &&
    _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET) != msg.sender
  ) {
    revert OnlyPartyDaoOrHostError(msg.sender);
  }
  emergencyExecuteDisabled = true;
  emit EmergencyExecuteDisabled();
}
`

Which just means that the host decides on if they want to allow an emergency execution, since the host can just listen to the mempool and call `disableEmergencyExecute` to preemptively block any of the DAO's emergency action.

### Recommended Mitigation Steps

Add a time lock to the disabling function, fix is perfect cause the `emergencyExecute()` is to be implemented for critical situations so a host can't front run the execution.

## QA-02 Misusal of `_rageQuit()` could heavily impact proposal outcomes

### Impact

Someone with a large enough holding calling `rageQuit()` could potentially decide the outcome of votes, and this could impact more that the votes of the caller, cause just as human nature a user that might have voted could see that enough votes is present and decide on not voting just for someone else to call `rageQuit()` at the late stages of the voting process.

### Proof of Concept

The `_rageQuit` function, intended as a safeguard against 51% attacks, may be exploited by party members who choose to exit the party, thereby impacting the outcomes of ongoing proposals. Members holding significant voting power can sway decision-making processes by participating in a vote and then withdrawing their share, possibly after deducing that their vote could decisively influenced the outcome. This could undermine the integrity of the voting process, as members may have been influenced by the weight of votes at a given time*(human nature)*, this could be misleading if `rageQuit` is executed subsequently.

Take a look at: [PartyGovernanceNFT.sol#L344-L448](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L344-L448)

The `rageQuit` function allows a party member to burn their NFT in exchange for their share of the party's funds, corresponding to their voting power. The problematic scenario unfolds as follows:

1. A party member with substantial voting power participates in a vote, potentially swaying other members to vote in a certain way or deter them from voting, believing the outcome is already decided.
2. After influencing the vote, the member executes `rageQuit` right at the brim of the voting process, removing their funds and voting power from the party.
3. The proposal, which might have reached a decision threshold with the member's vote, is now left in a state that does not reflect the true consensus of the remaining members.
4. Other members, who might have abstained from voting, can no longer vote due to time restraint, effectively having the proposal's decision not representing the majority's will.

### Recommended Mitigation Steps

Have a time buffer, i.e do not accept a `rageQuit()` around the end of a proposal's voting process.

## QA-03 Remove redundant naming convention for custom errors

### Impact

The use of the term "Error" appended to custom error names in the `PartyGovernanceNFT` smart contract is redundant. Solidity's revert statements already indicate that an error condition has been met, making the additional "Error" suffix unnecessary. This redundancy does not impact the functionality or the security of the contract, but it does affect the readability and conciseness of the code.

### Proof of Concept

Here are the custom errors defined in the contract, _[source](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L20-L25)_.

```solidity
error FixedRageQuitTimestampError(uint40 rageQuitTimestamp);
error CannotRageQuitError(uint40 rageQuitTimestamp);
error CannotDisableRageQuitAfterInitializationError();
error InvalidTokenOrderError();
error BelowMinWithdrawAmountError(uint256 amount, uint256 minAmount);
error NothingToBurnError();
```

Solidity's error handling mechanism is explicit in signifying that an error condition has been triggered. Custom errors serve as a mechanism for signalling specific conditions without necessarily needing to state they are errors. The term "Error" is therefore extraneous, as demonstrated by the standard naming practices in widely-accepted EIPs and the broader Solidity community.

### Recommended Mitigation Steps

The contract should remove the "Error" suffix from custom error names to improve clarity. The updated error names should succinctly describe the condition that triggers them without the redundancy of the "Error" term. Here are the revised custom error names:

```solidity
error FixedRageQuitTimestamp(uint40 rageQuitTimestamp);
error CannotRageQuit(uint40 rageQuitTimestamp);
error CannotDisableRageQuitAfterInitialization();
error InvalidTokenOrder();
error BelowMinWithdrawAmount(uint256 amount, uint256 minAmount);
error NothingToBurn();
```

Refactoring the error names as suggested will make the code cleaner and align with common Solidity naming conventions. It's a small change that contributes to better code quality and maintainability.

## QA-04 `rageQuit()'s` execution window could be inaccurate

### Impact

The `rageQuit` function in the smart contract allows users to burn governance NFTs and withdraw their share of fungible tokens. However, the current implementation permits a user to execute `rageQuit` at the exact `block.timestamp` that marks the end of the permitted window, which is inconsistent with the expected behaviour. This may allow an action to be taken when it should theoretically be disallowed, potentially leading to governance issues or disputes among token holders.

### Proof of Concept

Take a look at [rageQuit()](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L344)

```solidity
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
                    //@audit would pass when currentRageQuitTimestamp == block.timestamp
                    revert CannotRageQuitError(currentRageQuitTimestamp);
                }
                //... omitted for brevity
            }
```

As seen, the code block includes a conditional that is intended to prevent the function from executing in regards to `currentRageQuitTimestamp`.

However, as per the current code, users can still perform a rage quit at the exact `block.timestamp` matching `currentRageQuitTimestamp`, which is not the desired logic.

### Recommended Mitigation Steps

Easy fix, make the conditional check inclusive.

```diff
if (
    currentRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY ||
-    currentRageQuitTimestamp < block.timestamp // Corrected condition
+    currentRageQuitTimestamp <= block.timestamp // Corrected condition
) {
    revert CannotRageQuitError(currentRageQuitTimestamp);
}
```

By making this change, the `rageQuit` function will properly prevent users from executing the function at or after the exact timestamp that marks the cutoff, ensuring the smart contract behaves as intended and documented. Furthermore, the documentation should be revised to accurately describe the behaviour and restrictions of the `rageQuit` function.

> NB: Submitted this as QA as it depends on how the team really view the invariant on how deadlines and not accepting `rageQuit` at `currentRageQuitTimestamp`. If protocol _MUST NOT_ accept `rageQuit()` when `currentRageQuitTimestamp == block.timestamp` then this imo should be upgraded.

Unreated to this, do note that the `PartyGovernance.cancel()` is also vulnerable in the same context to this,

```solidity
            uint256 cancelTime = values.executedTime + cancelDelay;
            // Must not be too early.
            if (block.timestamp < cancelTime) {
                revert ProposalCannotBeCancelledYetError(
                    uint40(block.timestamp),
                    uint40(cancelTime)
                );
```

The check should be inclusive to ensure that cancel time is not too early, i.e the changes below should be applied

```diff
            uint256 cancelTime = values.executedTime + cancelDelay;
            // Must not be too early.
-            if (block.timestamp < cancelTime) {
+            if (block.timestamp <= cancelTime) {
                revert ProposalCannotBeCancelledYetError(
                    uint40(block.timestamp),
                    uint40(cancelTime)
                );
```

## QA-05 Some safety measures should be implemented before deployment on BASE

### Impact

The impact is currently categorized as low, but this is a significant issue on Base where not all opcodes available on the mainnet are supported. A key example is the lack of the PUSH0 opcode.

> Note: This issue is erroneously listed in the _disputed_ section of the bot report, but it remains valid and unresolved.

### Proof of Concept

The official BASE documentation explicitly states the discrepancy:

> "Base Mainnet and Testnet do not yet support the new PUSH0 opcode introduced in Shanghai. The Solidity compiler defaults to this opcode if version 0.8.20 or later is used. It is recommended to use version 0.8.19 or earlier until an upgrade occurs."

Despite this clear guideline, a review of contracts both in and out of scope reveals that many are compiled using version `0.8.20`, which directly contravenes the recommended safety measures.

### Recommended Mitigation Steps

To ensure safety and compatibility, it's crucial to adhere strictly to the recommendations provided by the blockchain platforms intended for deployment. In this case, contracts should be compiled using Solidity version `0.8.19` or lower until BASE updates its support for the PUSH0 opcode.

## QA-06 Inconsistency in sister function implementations

### Impact

Both `batchContribute` and `batchContributeFor` could be considered sister functions. Issue is now that while `batchContribute` handles the difference between the total ETH sent and the actual ETH used in contributions by refunding the unused portion, `batchContributeFor` does not follow the same logic and instead reverts if the ETH sent does not match the exact sum of the contributions. This inconsistency easily makes executing `batchContributeFor` way harder as having a more relaxed conditional would be bets and then a refund is made of the extras.
`

### Proof of Concept

The `batchContribute()` function allows for a user to send more ETH than needed and refunds the unused portion:

```solidity
function batchContribute(
  BatchContributeArgs calldata args
) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {
  // ...
  if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);
}
```

However, the `batchContributeFor()` function requires the exact ETH amount to match the sum of the contributions:

```solidity
function batchContributeFor(
  BatchContributeForArgs calldata args
) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {
  // ...
  if (msg.value != valuesSum) {
    revert InvalidMessageValue();
  }
}
```

The discrepancy between these two functions leads to not only weird behaviour for users who are accustomed to the refund logic in `batchContribute` and attempt to use `batchContributeFor` in a similar manner, but would even harden the whole process of executing `batchContributeFor`

### Recommended Mitigation Steps

Align the implementation of `batchContributeFor` with `batchContribute` by including logic to handle and refund any excess ETH sent.

## QA-07 `signingThersholdBps` should be `signingThresholdBps` instead

### Impact

Low, info, currently the typographical error in the variable name `signingThersholdBps` used in the mapping declaration can lead to confusion and potential errors during future maintenance or upgrades of the contract. The incorrect spelling is inconsistent with standard naming conventions in a logical sense and may cause misunderstandings or miscommunication between developers.

> NB: This variable name is not a breaking issue for the current implementation, but it is considered poor practice and goes against the clarity and readability of the code.

### Proof of Concept

The mapping declaration is intended to track the signing threshold basis points (BPS) for each party. The current spelling in the code is `signingThersholdBps` where it should be `signingThresholdBps`. This is a clear typographical error.

Using the the `OffChainSignatureValidator` contract as a case study, t he incorrect variable name is present in two places

1. In the mapping declaration:
   ```solidity
   mapping(Party => uint96) public signingThersholdBps;
   ```
2. Inside the `setSigningThresholdBps` function:
   ```solidity
   emit SigningThresholdBpsSet(party, signingThersholdBps[party], thresholdBps);
   signingThersholdBps[party] = thresholdBps;
   ```

### Recommended Mitigation Steps

Easy fix, rename the mapping from `signingThersholdBps` to `signingThresholdBps` throughout the project's scope.

For `OffChainSignatureValidator.sol`:

```solidity
mapping(Party => uint96) public signingThresholdBps;

...

emit SigningThresholdBpsSet(party, signingThresholdBps[party], thresholdBps);
signingThresholdBps[party] = thresholdBps;
```

## QA-08 `burn` should include a similar check like the `prevToken >= withdrawTokens` from `rageQuit()`

### Impact

Low

The `burn()` function allows for the burning of NFTs. If the same `tokenId` is included more than once in the `tokenIds` array, the function will revert upon the second attempt to burn the already deleted token. This is because the `_burn()` internal function, which is called within `_burnAndUpdateVotingPower`, deletes the token's data, and subsequent calls to `ownerOf(tokenId)` will fail as the token no longer exists. This considerably makes attemots to burn to be harder

### Proof of Concept

1. A call to the `burn` function with an array of `tokenIds` that includes duplicates will result in a successful burn of the token on the first iteration.
   Keep `_burn()` in mind:

```solidity
function _burn(uint256 id) internal virtual {
  address owner = _ownerOf[id];

  require(owner != address(0), "NOT_MINTED");

  // Ownership check above ensures no underflow.
  unchecked {
    _balanceOf[owner]--;
  }

  delete _ownerOf[id];

  delete getApproved[id];

  emit Transfer(owner, address(0), id);
}
```

2. On the second iteration, when trying to burn the same tokenId again, the function call to `ownerOf(tokenId)` will revert since the token has already been burned and its ownership information deleted.

3. The smart contract code does not currently check for duplicate `tokenIds` in the input array before attempting to burn them.

The potential for reversion is due to these lines:

```solidity
function _burnAndUpdateVotingPower(
    uint256[] memory tokenIds,
    bool checkIfAuthorizedToBurn
) private returns (uint96 totalVotingPowerBurned) {
    for (uint256 i; i < tokenIds.length; ++i) {
        uint256 tokenId = tokenIds[i];
        address owner = ownerOf(tokenId); // <- Reverts here if tokenId was already burned
        ...
        _burn(tokenId); // <- Burns the token and deletes ownerOf mapping
        ...
    }
    ...
}
```

### Recommended Mitigation Steps

An easy fix would be to introduce a similar check like the one present in `rageQuit()`, i.e:

`if (prevToken >= withdrawTokens[i]) revert InvalidTokenOrderError();`

## QA-09 Self delegation mechanism seems to be flawed

### Impact

Potentially hijacking of the self-delegation process in the context of crowdfund contributions, i.e an attacker can effectively redirect the voting power of NFTs, minted post-crowdfund, to themselves, which undermines the integrity of the voting process, potentially leading to a significant shift in control and decision-making within the governance structure.

> NB: This issue was previously submitted [here](https://github.com/code-423n4/2023-04-party-findings/issues/38), but the recommended fix was never applied, during the course of the contest, sponsors did not clarify if this is an accepted risk or not, but since previous reports were linked, don't know if this is to be considered `Known Issue`.

### Proof of Concept

This is hinted from the interaction between the self-delegation logic in `PartyGovernance.sol` and the contribution processing in `ETHCrowdfundBase.sol`.

- In `PartyGovernance.sol`, self-delegation is intended to occur when a user's delegate is `address(0)`. This logic assumes that a user delegating to `address(0)` implies a desire for self-delegation.

- However, in `ETHCrowdfundBase.sol`, during contribution processing `_processContribution()`, a contributor can specify a delegate. If a user has not previously delegated their vote or if the contribution is made by the contributor themselves, the specified delegate is set.
  - Issue is that, potentially if a contribution is made on behalf of someone else (e.g., by an attacker), and the target user is currently self-delegated, this logic incorrectly allows the attacker to specify a new delegate, effectively hijacking the target's delegation.

### Recommended Mitigation Steps

Modify the logic in `ETHCrowdfundBase.sol` to ensure that if a contribution is made by a user for themselves with a delegate of `address(0)`, it automatically results in self-delegation. This can be done by adding a check to see if `msg.sender` is the same as the `contributor` and `delegate` is `address(0)`, then setting the delegate to the `contributor`.

## QA-10 Fix contextual errors in docs

### Impact

Low, info on better code documentation.

### Proof of Concept

Take a look at [contribute()](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L185)

```solidity
function contribute(
  address delegate,
  bytes memory gateData
) public payable onlyDelegateCall returns (uint96 votingPower) {
  return
    _contribute(
      payable(msg.sender),
      delegate,
      msg.value.safeCastUint256ToUint96(),
      0, // Mint a new party card for the contributor.
      gateData
    );
}
```

It's been explained in the [docs for InitialETHCrowdfund.sol](https://docs.partydao.org/docs/crowdfund/InitialETHCrowdfund) as this:

```
contribute
Contribute ETH to this crowdfund on behalf of a contributor.
```

But this is wrong cause this is instead the explanation for the `contributeFor()`.

### Recommended Mitigation Steps

Ensure that explanation really translate to how code is intended to be used.
