## [L-01] Potential bypass token ownership

The contribute() and contributeFor() functions do not check that the supplied tokenId is valid (minted) or owned by the calling address.An attacker could call these functions with any tokenId, even if they do not own that token.This would allow minting new voting power or increasing existing voting power without actually owning the token.This stolen voting power could then be used to influence governance outcomes.

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

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L164

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L235

**Recommendations**

Add checks in contribute() and contributeFor() that the tokenId is valid and owned by the calling address before updating voting power.

## [L-02] Incorrect voting power due to unvalidated ETH values

The contribute(), contributeFor() and batchContributeFor() functions take an 'amount' parameter but do not validate it against the actual msg.value.If msg.value does not equal the specified 'amount', the voting power calculation in `_processContribution()` will be incorrect.This could result in contributors receiving more or less voting power than intended.

```solidity
File: crowdfund/InitialETHCrowdfund.sol


    function contribute(

    function contributeFor(

    function batchContributeFor(
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L255

**Recommendations:**

Add validation in contribute(), contributeFor() and batchContributeFor() to check msg.value equals the 'amount' parameter.

## [L-03] The InitialETHCrowdfund::refund function does not validate that the caller owns the tokenId being refunded

The refund() function does not validate that the caller owns the tokenId being refunded.Anyone can call refund() with any tokenId, even if they do not own it.This burns the token and transfers the refund amount to the caller. This effectively bypasses token ownership, allowing theft of refund funds.

the missing ownership check in refund() is a critical issue as it allows anyone to steal refund funds by bypassing token ownership. Proper validation is needed before manipulating tokens or balances.

```solidity
    function refund(uint256 tokenId) external returns (uint96 amount) {
        // Check crowdfund lifecycle.
        {
            CrowdfundLifecycle lc = getCrowdfundLifecycle();
            if (lc != CrowdfundLifecycle.Lost) {
                revert WrongLifecycleError(lc);
            }
        }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L318C1-L325C10

## [L-04] Missing validation of token ownership in transfer functions

The transferFrom(), safeTransferFrom() functions do not validate that the caller/sender is the owner of the token being transferred. This could allow tokens to be transferred without ownership.

```solidity
    function transferFrom(address owner, address to, uint256 tokenId) public override {
        // Transfer voting along with token.
        _transferVotingPower(owner, to, votingPowerByTokenId[tokenId]);
        super.transferFrom(owner, to, tokenId);
    }

    /// @inheritdoc ERC721
    function safeTransferFrom(address owner, address to, uint256 tokenId) public override {
        // super.safeTransferFrom() will call transferFrom() first which will
        // transfer voting power.
        super.safeTransferFrom(owner, to, tokenId);
    }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L451C1-L463C1

## [L-05] The PartyGovernanceNFT::rageQuitTimestamp can be set to permanently disable ragequits without restrictions.

The setRageQuit() function allows setting the rageQuitTimestamp value, which determines if ragequit is enabled or disabled.There are no authorization checks, so anyone could call this function.

```solidity
    function setRageQuit(uint40 newRageQuitTimestamp) external {
        _assertHost();
        // Prevent disabling ragequit after initialization.
        if (newRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY) {
            revert CannotDisableRageQuitAfterInitializationError();
        }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L318C1-L323C10

The timestamp can be set to special constant values like DISABLE_RAGEQUIT_PERMANENTLY (0xab2cb21860) that permanently disable ragequit.Once set to this value, ragequit would never be possible again even if the party or governance wanted to re-enable it in the future.Users would not be able to ragequit or withdraw their portion of funds even if they no longer wanted to participate. This could effectively lock their tokens indefinitely.While initialized can only disable permanently, after initialization anyone could call it to disable ragequit at any time, going against the intentions of the governance.A malicious actor could deliberately disable ragequit to trap users' funds/ tokens within the system against their will.There are no safeguards like a time lock or confirmation vote required before disabling ragequit permanently.Even authorities have unlimited control to lock all participants out of ragequitting at any time via this function call.

## [L-06] State manipulation without auth

The `_getStorage`() function allow directly reading and modifying the internal state variables without any access controls.

```solidity
    function _getStorage() internal pure returns (Storage storage stor) {
        uint256 slot = _STORAGE_SLOT;
        assembly {
            stor.slot := slot
        }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L335C1-L340C6

For example, setCurrentInProgressProposalId simply sets the value of currentInProgressProposalId state variable.

Anyone can call this function via delegatecall to directly modify this critical state.Similarly other functions like cancelProposal(), directly clear the in-progress state.

```solidity
    function cancelProposal(uint256 proposalId) external onlyDelegateCall {
        // Must be a valid proposal ID.
        if (proposalId == 0) {
            revert ZeroProposalIdError();
        }
        Storage storage stor = _getStorage();
        {
            // Must be the current InProgress proposal.
            uint256 currentInProgressProposalId = stor.currentInProgressProposalId;
            if (currentInProgressProposalId != proposalId) {
                revert ProposalNotInProgressError(proposalId);
            }
        }
        // Clear the current InProgress proposal ID and next progress data.
        stor.currentInProgressProposalId = 0;
        stor.nextProgressDataHash = 0;
    }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L207C1-L223C6

This breaks an important security principle that state should only be modified with authorization.A malicious actor could exploit this to bypass proposal execution flow by directly modifying state.

For example, they could set the in-progress ID to bypass locking logic and execute multiple in parallel.Or clear the in-progress ID to cancel a vote before it finalizes.

Proper access controls are needed, like only allowing party contract to modify state.Or adding a require to check caller is authorized before all state modifications.Without this, an upgraded/hijacked proposal contract could manipulate critical governance state.

## [L-07] The `_finalize()` function transfers ETH to the party contract before updating storage variables like totalContributions

The finalize() function transfers ETH to the party contract before updating storage variables like totalContributions. This allows the party contract to potentially call back into the crowdfund contract in a reentrant manner. If such a callback modified state, it could interfere with the execution of finalize().

To exploit this, the party contract would need to be malicious or compromised. It could call finalize(), get the funds, and then call another function like contribute more ETH with an unintended leftover balance. Proper reentrancy mitigations like reverting state first are needed.

```solidity
File:  crowdfund/ETHCrowdfundBase.sol

    function _finalize(uint96 totalContributions_) internal {
        // Finalize the crowdfund.
        delete expiry;

        // Transfer funding split to recipient if applicable.
        address payable fundingSplitRecipient_ = fundingSplitRecipient;
        uint16 fundingSplitBps_ = fundingSplitBps;
        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
            totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
        }

        // Update the party's total voting power.
        uint96 newVotingPower = (totalContributions_ * exchangeRateBps) / 1e4;
        party.increaseTotalVotingPower(newVotingPower);

        // Transfer ETH to the party.
        payable(address(party)).transferEth(totalContributions_);

        emit Finalized();
    }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L291-L336


