# GAS OPTIMIZATIONS

## Findings

- [[G-1] Use global variable rather than caching global variable](#)
- [[G-2]  Use cache value instead of new read](#)
- [[G-3] Optimize gas usage by avoiding variable declarations inside loops](#)
- [[G-4] Optimize event for better gas optimization](#)
- [[G-5] Use Gas optimized version of `IERC2981` and `IERC1271` library instead of openzeppelin library](#)
- [[G-6] Do not cache state variable if used once](#)
- [[G-7] **Optimize the `sendFndingSplit()` function for better gas efficiency**](#)
- [[G-8] Low level `call` can be optimized with assembly](#)

### [G-01] Use global variable rather than caching global variable

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L209C1-L212C44

```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
204: function batchContribute(
        BatchContributeArgs calldata args
    ) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {
        uint256 numContributions = args.tokenIds.length;
        votingPowers = new uint96[](numContributions);

        uint256 ethAvailable = msg.value; //@audit Do Not Cache global variable
        for (uint256 i; i < numContributions; ++i) {
            ethAvailable -= args.values[i];

            votingPowers[i] = _contribute(
                payable(msg.sender),
                args.delegate,
                args.values[i],
                args.tokenIds[i],
                args.gateDatas[i]
            );
        }

        // Refund any unused ETH.
        if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);
    }
```

```diff
File: contracts/crowdfund/InitialETHCrowdfund.sol
204: function batchContribute(
        BatchContributeArgs calldata args
    ) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {
        uint256 numContributions = args.tokenIds.length;
        votingPowers = new uint96[](numContributions);

-        uint256 ethAvailable = msg.value; 
+        uint256 ethAvailable;
        for (uint256 i; i < numContributions; ++i) {
-            ethAvailable -= args.values[i];
+            ethAvailable = msg.value - args.values[i]
            votingPowers[i] = _contribute(
                payable(msg.sender),
                args.delegate,
                args.values[i],
                args.tokenIds[i],
                args.gateDatas[i]
            );
        }

        // Refund any unused ETH.
-        if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);
+        if (msg.value > 0) payable(msg.sender).transfer(msg.value);
    }
```

### [G-02] Use cache value instead of new read

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L223C5-L223C42

```solidity
File: contracts/party/PartyGovernanceNFT.sol
208: function increaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        _assertAuthority();
        uint96 mintedVotingPower_ = mintedVotingPower;
        uint96 totalVotingPower = _getSharedProposalStorage().governanceValues.totalVotingPower;

        // Cap voting power to remaining unminted voting power supply. Allow
        // minting past total voting power if minting party cards for initial
        // crowdfund when there is no total voting power.
        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower) {
            unchecked {
                votingPower = totalVotingPower - mintedVotingPower_;
            }
        }

        // Update state.
        mintedVotingPower += votingPower; //@audit Use cache value
        uint256 newIntrinsicVotingPower = votingPowerByTokenId[tokenId] + votingPower;
        votingPowerByTokenId[tokenId] = newIntrinsicVotingPower;

        emit PartyCardIntrinsicVotingPowerSet(tokenId, newIntrinsicVotingPower);

        _adjustVotingPower(ownerOf(tokenId), votingPower.safeCastUint96ToInt192(), address(0));
    }
```

```diff
File: contracts/party/PartyGovernanceNFT.sol
208: function increaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        _assertAuthority();
        uint96 mintedVotingPower_ = mintedVotingPower;
        uint96 totalVotingPower = _getSharedProposalStorage().governanceValues.totalVotingPower;

        // Cap voting power to remaining unminted voting power supply. Allow
        // minting past total voting power if minting party cards for initial
        // crowdfund when there is no total voting power.
        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower) {
            unchecked {
                votingPower = totalVotingPower - mintedVotingPower_;
            }
        }

        // Update state.
-        mintedVotingPower += votingPower;
+        mintedVotingPower =  mintedVotingPower + votingPower;
        uint256 newIntrinsicVotingPower = votingPowerByTokenId[tokenId] + votingPower;
        votingPowerByTokenId[tokenId] = newIntrinsicVotingPower;

        emit PartyCardIntrinsicVotingPowerSet(tokenId, newIntrinsicVotingPower);

        _adjustVotingPower(ownerOf(tokenId), votingPower.safeCastUint96ToInt192(), address(0));
    }
```

### [G-03] Optimize gas usage by avoiding variable declarations inside loops

The variables `tokenId`, `owner`, `token`, and `amount` are all declared inside the loop. This means that a new instance of each variable will be created for each iteration of the loop. Saves `200-300 Gas` per iteration

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L273C6-L274C46

```solidity
File: contracts/party/PartyGovernanceNFT.sol
273: uint256 tokenId = tokenIds[i]; //@audit
274: address owner = ownerOf(tokenId);
```

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L408C13-L410C53

```solidity
File: contracts/party/PartyGovernanceNFT.sol
409: IERC20 token = withdrawTokens[i]; //@audit
410: uint256 amount = withdrawAmounts[i];
```

### [G-04] optimize event for better gas optimization

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L334C1-L335C2

```solidity
File: contracts/party/PartyGovernanceNFT.sol
318: function setRageQuit(uint40 newRageQuitTimestamp) external {
        _assertHost();
        // Prevent disabling ragequit after initialization.
        if (newRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY) {
            revert CannotDisableRageQuitAfterInitializationError();
        }

        uint40 oldRageQuitTimestamp = rageQuitTimestamp;

        // Prevent setting timestamp if it is permanently enabled/disabled.
        if (
            oldRageQuitTimestamp == ENABLE_RAGEQUIT_PERMANENTLY ||
            oldRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY
        ) {
            revert FixedRageQuitTimestampError(oldRageQuitTimestamp);
        }

        emit RageQuitSet(oldRageQuitTimestamp, rageQuitTimestamp = newRageQuitTimestamp); //@audit rageQuitTimestamp
    } 
```

```diff
File: contracts/party/PartyGovernanceNFT.sol
318: function setRageQuit(uint40 newRageQuitTimestamp) external {
        _assertHost();
        // Prevent disabling ragequit after initialization.
        if (newRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY) {
            revert CannotDisableRageQuitAfterInitializationError();
        }

        uint40 oldRageQuitTimestamp = rageQuitTimestamp;
        rageQuitTimestamp = newRageQuitTimestamp

        // Prevent setting timestamp if it is permanently enabled/disabled.
        if (
            oldRageQuitTimestamp == ENABLE_RAGEQUIT_PERMANENTLY ||
            oldRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY
        ) {
            revert FixedRageQuitTimestampError(oldRageQuitTimestamp);
        }
-
-        emit RageQuitSet(oldRageQuitTimestamp, rageQuitTimestamp = newRageQuitTimestamp); 
+        emit RageQuitSet(oldRageQuitTimestamp, newRageQuitTimestamp);   
 } 
```

### [G-05] Use Gas optimized version of `IERC2981` and `IERC1271` library instead of openzeppelin library

Solady’s [IERC2981](https://github.com/Vectorized/solady/blob/main/src/utils/SafeCastLib.sol) implementation is more gas efficient than OZ's version. I recommend to switch to that library instead

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L6

```solidity
File: contracts/party/PartyGovernanceNFT.sol
6: import "openzeppelin/contracts/interfaces/IERC2981.sol"; //@audit Use solady library's
```

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L7

```solidity
File: contracts/proposals/ProposalExecutionEngine.sol
7: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol"; //@audit Use solady library's
```

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L4

```solidity
File: contracts/signature-validators/OffChainSignatureValidator.sol
4: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol"; //@audit Use solady library's
```

### [G-06] Do not cache state variable if used once

If function use variable only once then it doesn’t make sense to cache that variable in memory it just waste of gas.Recommeded avoid such allocation.

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L263C8-L263C76

```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
261: address payable fundingSplitRecipient_ = fundingSplitRecipient; //@audit
        uint16 fundingSplitBps_ = fundingSplitBps;
        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
            // Removes funding split from contribution amount in a way that
            // avoids rounding errors for very small contributions <1e4 wei.
            amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
        }
```

```diff
File: contracts/crowdfund/ETHCrowdfundBase.sol
-261: address payable fundingSplitRecipient_ = fundingSplitRecipient; //@audit
        uint16 fundingSplitBps_ = fundingSplitBps;
-        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
+        if (fundingSplitRecipient != address(0) && fundingSplitBps_ > 0) {
            // Removes funding split from contribution amount in a way that
            // avoids rounding errors for very small contributions <1e4 wei.
            amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
        }
```

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L324

```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
322: address payable fundingSplitRecipient_ = fundingSplitRecipient; //@audit
        uint16 fundingSplitBps_ = fundingSplitBps;
        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
            totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
        }
```

```diff
File: contracts/crowdfund/ETHCrowdfundBase.sol
-322: address payable fundingSplitRecipient_ = fundingSplitRecipient; //@audit
        uint16 fundingSplitBps_ = fundingSplitBps;
-        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
+        if (fundingSplitRecipient != address(0) && fundingSplitBps_ > 0) {
            totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
        }
```

### [G-07] **Optimize the `sendFndingSplit()` function for better gas efficiency**

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L344

```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
339: function sendFundingSplit() external returns (uint96 splitAmount) {
        // Check that the crowdfund is finalized.
        CrowdfundLifecycle lc = getCrowdfundLifecycle();
        if (lc != CrowdfundLifecycle.Finalized) revert WrongLifecycleError(lc);

        if (fundingSplitPaid) revert FundingSplitAlreadyPaidError(); //@audit Revert first

        address payable fundingSplitRecipient_ = fundingSplitRecipient;
        uint16 fundingSplitBps_ = fundingSplitBps;
        if (fundingSplitRecipient_ == address(0) || fundingSplitBps_ == 0) {
            revert FundingSplitNotConfiguredError();
        }

        fundingSplitPaid = true;

        // Transfer funding split to recipient.
        splitAmount = (totalContributions * fundingSplitBps_) / 1e4;
        payable(fundingSplitRecipient_).transferEth(splitAmount);

        emit FundingSplitSent(fundingSplitRecipient_, splitAmount);
    }
```

```diff
File: contracts/crowdfund/ETHCrowdfundBase.sol
339: function sendFundingSplit() external returns (uint96 splitAmount) {
        // Check that the crowdfund is finalized.
+      if (fundingSplitPaid) revert FundingSplitAlreadyPaidError();
        CrowdfundLifecycle lc = getCrowdfundLifecycle();
        if (lc != CrowdfundLifecycle.Finalized) revert WrongLifecycleError(lc);

-       if (fundingSplitPaid) revert FundingSplitAlreadyPaidError(); 
        address payable fundingSplitRecipient_ = fundingSplitRecipient;
        uint16 fundingSplitBps_ = fundingSplitBps;
        if (fundingSplitRecipient_ == address(0) || fundingSplitBps_ == 0) {
            revert FundingSplitNotConfiguredError();
        }

        fundingSplitPaid = true;

        // Transfer funding split to recipient.
        splitAmount = (totalContributions * fundingSplitBps_) / 1e4;
        payable(fundingSplitRecipient_).transferEth(splitAmount);

        emit FundingSplitSent(fundingSplitRecipient_, splitAmount);
    }
```

### [G-08] Low level `call` can be optimized with assembly

When using low-level calls, the `returnData` is copied to memory even if the variable is not utilized. The proper way to handle this is through a low level assembly call. For example:

```solidity
(bool success,) = payable(receiver).call{gas: gas, value: value}("");
```

can be optimized to:

```solidity
bool success;
assembly {
    success := call(gas, receiver, value, 0, 0, 0, 0)
}
```

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L379

```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol
379: (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData); //@audit
        if (!success) {
            res.rawRevert();
        }0
```

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L358

```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
358: (bool s, bytes memory r) = address(this).call( //@audit
                abi.encodeCall(this.refund, (tokenIds[i]))
            ); 
```

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L846

```solidity
File: ontracts/party/PartyGovernance.sol
846: (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData); //@audit
```