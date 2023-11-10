## Gas Optimizations
| Number | Issue | Instances |
|-|:-|:-:|
| [[G-01](#g-01-used-already-cached-value-of-state-variable-rather-than-re-reading-from-storage)] | Used already cached value of `state variable` rather than re-reading from storage | 1 |
| [[G-02](#g-02-dont-cache-state-variable-if-it-is-used-only-once)] | Don't cache `state` variable if it is used only once | 6 |
| [[G-03](#g-03-dont-emit-state-variable)] | Don't `emit` `state` variable | 1 |
| [[G-04](#g-04-do-not-increase-optsauthoritieslength-with-1)] | Do not increase `opts.authorities.length` with 1 | 1 |
| [[G-05](#g-05-use-constant-instead-of-typexmax-to-save-gas)] | Use constant instead of `type(x).max` to save gas | 12 |
| [[G-06](#g-06-check-ethamount-for-zero-before-transfer)] | Check `Eth`/`amount` for `zero` before transfer | 2 |
| [[G-07](#g-07-remove-unused-errorevent)] | Remove unused `error`/`event` | 5 |
| [[G-08](#g-08-can-make-the-variable-outside-the-loop-to-save-gas)] | Can Make The Variable Outside The Loop To Save Gas | 9 |


## [G-01] Used already cached value of `state variable` rather than re-reading from storage

_1 Instance in 1 File_

```solidity
File : contracts/crowdfund/ETHCrowdfundBase.sol

227:        uint96 newTotalContributions = totalContributions + amount;
228:        uint96 maxTotalContributions_ = maxTotalContributions;
229:        if (newTotalContributions >= maxTotalContributions_) {
230:            totalContributions = maxTotalContributions_;
231:
232:            // Finalize the crowdfund.
233:            // This occurs before refunding excess contribution to act as a
234:            // reentrancy guard.
235:            _finalize(maxTotalContributions_);
236:
237:            // Refund excess contribution.
238:            uint96 refundAmount = newTotalContributions - maxTotalContributions;

```
[227-238](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L227C1-L238C81)

```diff
File : contracts/crowdfund/ETHCrowdfundBase.sol

        uint96 newTotalContributions = totalContributions + amount;
        uint96 maxTotalContributions_ = maxTotalContributions;
        if (newTotalContributions >= maxTotalContributions_) {
            totalContributions = maxTotalContributions_;

            // Finalize the crowdfund.
            // This occurs before refunding excess contribution to act as a
            // reentrancy guard.
            _finalize(maxTotalContributions_);

            // Refund excess contribution.
-           uint96 refundAmount = newTotalContributions - maxTotalContributions;
+           uint96 refundAmount = newTotalContributions - maxTotalContributions_;

```

## [G-02] Don't cache `state` variable if it is used only once

_6 Instances in 4 Files_

<details>
<summary>see instances</summary>

```solidity
File : contracts/party/PartyGovernance.sol

1033:      if (n != 0) {
1034:          VotingPowerSnapshot memory lastSnap = voterSnaps[n - 1]; //@audit Don't cache `voterSnaps[n - 1]`
1035:          if (lastSnap.timestamp == snap.timestamp) {
1036:              voterSnaps[n - 1] = snap;
1037:              return;
1038:           }
1039:      }

```
[1033-1039](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1033C1-L1039C10)

```solidity
File : contracts/proposals/ProposalExecutionEngine.sol

215:       uint256 currentInProgressProposalId = stor.currentInProgressProposalId; //@audit don't cache `stor.currentInProgressProposalId`
216:        if (currentInProgressProposalId != proposalId) {
217:           revert ProposalNotInProgressError(proposalId);
218:         }
219:       }
220:        // Clear the current InProgress proposal ID and next progress data.
221:        stor.currentInProgressProposalId = 0;
222:        stor.nextProgressDataHash = 0;
223:    }

```
[215-223](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L215C1-L223C6)

```solidity
File : contracts/proposals/ProposalExecutionEngine.sol

336:      uint256 slot = _STORAGE_SLOT; //@audit don't cache `_STORAGE_SLOT`
337:      assembly {
338:          stor.slot := slot
339:        }

```
[336-339](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L336C8-L339C10)

```solidity
File : contracts/proposals/ProposalStorage.sol

45:        IProposalExecutionEngine oldImpl = stor.engineImpl;//@audit don't cache `stor.engineImpl`
46:        stor.engineImpl = impl;
47:        (bool s, bytes memory r) = address(impl).delegatecall(
48:            abi.encodeCall(IProposalExecutionEngine.initialize, (address(oldImpl), initData))
49:        );
50:        if (!s) {
51:            r.rawRevert();
52:        }
53:    }

```
[45-53](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L45C1-L53C6)

```solidity
File : contracts/proposals/ProposalStorage.sol

60:      uint256 s = SHARED_STORAGE_SLOT;//@audit don't cache `SHARED_STORAGE_SLOT`
61:      assembly {
62:          stor.slot := s
63:       }

```
[60-63](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L60C1-L63C10)

```solidity
File : contracts/proposals/SetSignatureValidatorProposal.sol

52:       uint256 slot = _SET_SIGNATURE_VALIDATOR_PROPOSAL_STORAGE_SLOT;//@audit don't cache `_SET_SIGNATURE_VALIDATOR_PROPOSAL_STORAGE_SLOT`
53:       assembly {
54:           stor.slot := slot
55:       }

```
[52-55](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L52C1-L55C10)

</details>

## [G-03] Don't `emit` `state` variable 

_1 Instance in 1 File_

```solidity
File : contracts/party/PartyGovernanceNFT.sol

333:      }
334:
335:       emit RageQuitSet(oldRageQuitTimestamp, rageQuitTimestamp = newRageQuitTimestamp);
336:    }

```
[333-336](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L333C1-L336C6)

```diff
File : contracts/party/PartyGovernanceNFT.sol

        }

+       rageQuitTimestamp = newRageQuitTimestamp;
+
-        emit RageQuitSet(oldRageQuitTimestamp, rageQuitTimestamp = newRageQuitTimestamp);
+        emit RageQuitSet(oldRageQuitTimestamp, newRageQuitTimestamp);
    }

```

## [G-04] Do not increase `opts.authorities.length` with 1  

If you can avoid unnecessary operations within a loop, it can potentially save gas. So directly cache `opts.authorities.length` in `authoritiesLength` and you created a new array `authorities` that is 1 element longer than `opts.authorities.length`.  

_1 Instance in 1 File_

```solidity
File : contracts/crowdfund/InitialETHCrowdfund.sol

377:        uint256 authoritiesLength = opts.authorities.length + 1;
378:        address[] memory authorities = new address[](authoritiesLength);
379:        for (uint i = 0; i < authoritiesLength - 1; ++i) {
380:            authorities[i] = opts.authorities[i];
381:        }
382:        authorities[authoritiesLength - 1] = address(this);

```
[377-382](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L377C1-L382C60)

```diff
File : contracts/crowdfund/InitialETHCrowdfund.sol

-        uint256 authoritiesLength = opts.authorities.length + 1;
-        address[] memory authorities = new address[](authoritiesLength);
-        for (uint i = 0; i < authoritiesLength - 1; ++i) {
+        uint256 authoritiesLength = opts.authorities.length;
+        address[] memory authorities = new address[](authoritiesLength + 1);
+        for (uint i = 0; i < authoritiesLength; ++i) {
            authorities[i] = opts.authorities[i];
        }
-        authorities[authoritiesLength - 1] = address(this);
+        authorities[authoritiesLength] = address(this);

```

## [G-05] Use constant instead of `type(x).max` to save gas

_12 Instances in 2 Files_

<details>
<summary>see instances</summary>

```solidity
File : contracts/party/PartyGovernance.sol

187:   if (govOpts.hosts.length > type(uint8).max) {

```
[187](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L187C5-L187C59)

```diff
File : contracts/party/PartyGovernance.sol

+    uint8 constant MAX_TYPE = type(uint8).max;
-    if (govOpts.hosts.length > type(uint8).max) {
+    if (govOpts.hosts.length > MAX_TYPE) {

```

```solidity
File : contracts/party/PartyGovernance.sol

359:   return getVotingPowerAt(voter, timestamp, type(uint256).max);

445:   return high == 0 ? type(uint256).max : high - 1;

520:   emit BatchMetadataUpdate(0, type(uint256).max);

581:   emit BatchMetadataUpdate(0, type(uint256).max);

655:   emit BatchMetadataUpdate(0, type(uint256).max);

688:   emit BatchMetadataUpdate(0, type(uint256).max);

833:   emit BatchMetadataUpdate(0, type(uint256).max);

897:   emit BatchMetadataUpdate(0, type(uint256).max);

926:   if (hintIndex != type(uint256).max) {

```
[359](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L359C9-L359C70), [445](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L445C8-L445C57), [520](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L520C8-L520C56), [581](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L581C9-L581C56), [655](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L655C13-L655C60),[688](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L688C9-L688C56), [833](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L833C9-L833C56), [897](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L897C8-L897C56), [926](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L926C13-L926C50)

```diff
File : contracts/party/PartyGovernance.sol

abstract contract PartyGovernance is
    ...

+      uint256 private constant MAX_UINT256 = type(uint256).max;
        ...

-      return getVotingPowerAt(voter, timestamp, type(uint256).max);
+      return getVotingPowerAt(voter, timestamp, MAX_UINT256);

-      return high == 0 ? type(uint256).max : high - 1;
+      return high == 0 ? MAX_UINT256 : high - 1;

-      emit BatchMetadataUpdate(0, type(uint256).max);
+      emit BatchMetadataUpdate(0, MAX_UINT256);

-      emit BatchMetadataUpdate(0, type(uint256).max);
+      emit BatchMetadataUpdate(0, MAX_UINT256);

-      emit BatchMetadataUpdate(0, type(uint256).max);
+      emit BatchMetadataUpdate(0, MAX_UINT256);

-      emit BatchMetadataUpdate(0, type(uint256).max);
+      emit BatchMetadataUpdate(0, MAX_UINT256);

-      emit BatchMetadataUpdate(0, type(uint256).max);
+      emit BatchMetadataUpdate(0, MAX_UINT256);

-      emit BatchMetadataUpdate(0, type(uint256).max);
+      emit BatchMetadataUpdate(0, MAX_UINT256);

-      if (hintIndex != type(uint256).max) {
+      if (hintIndex != MAX_UINT256) {

```

```solidity
File : contracts/party/PartyGovernance.sol

187:    uint96 private constant VETO_VALUE = type(uint96).max;

1083:   if (pv.votes == type(uint96).max) {

```
[1083](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1083C9-L1083C44)

```diff
File : contracts/party/PartyGovernance.sol

-    if (pv.votes == type(uint96).max) {
+    if (pv.votes == VETO_VALUE) {

```

```solidity
File : contracts/proposals/ProposalExecutionEngine.sol

185:    stor.nextProgressDataHash = bytes32(type(uint256).max);

```
[185](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L185C11-L185C68)

```diff
File : contracts/proposals/ProposalExecutionEngine.sol

+    uint256 constant MAX_TYPE = 2**256 - 1;
-    stor.nextProgressDataHash = bytes32(type(uint256).max);
+    stor.nextProgressDataHash = bytes32(MAX_TYPE);

```
</details>

## [G-06] Check `Eth`/`amount` for `zero` before transfer

_2 Instances in 1 File_

```solidity
File : contracts/crowdfund/ETHCrowdfundBase.sol

333:   payable(address(party)).transferEth(totalContributions_);//@audit check `totalContributions_`

356:   payable(fundingSplitRecipient_).transferEth(splitAmount);//@audit check `splitAmount`

```
[333](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L333C9-L333C66), [356](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L356C9-L356C66)

## [G-07] Remove unused `error`/`event` 

_5 Instances in 1 File_

<details>
<summary>see instances</summary>

```solidity
File : contracts/crowdfund/ETHCrowdfundBase.sol

51:    error NotAllowedByGateKeeperError(
52:        address contributor,
53:        IGateKeeper gateKeeper,
54:        bytes12 gateKeeperId,
55:        bytes gateData
56:     );

60:    error NotOwnerError(uint256 tokenId);

62:    error InvalidDelegateError();

69:    error ContributingForExistingCardDisabledError();

73:    error InvalidMessageValue();

```
[51-56](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L51C5-L56C7), [60](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L60C5-L60C42), [62](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L62C5-L62C34), [69](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L69C5-L69C54), [73](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L73C5-L73C33)

</details>

## [G-08] Can Make The Variable Outside The Loop To Save Gas

When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop.

_9 Instances in 3 Files_

<details>
<summary>see instances</summary>

```solidity
File : contracts/crowdfund/InitialETHCrowdfund.sol

357:    for (uint256 i; i < numRefunds; ++i) {
358:        (bool s, bytes memory r) = address(this).call(

```
[357-358](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L357C8-L358C59)

```diff
File : contracts/crowdfund/InitialETHCrowdfund.sol

+   bool s;
+   bytes memory r;
    for (uint256 i; i < numRefunds; ++i) {
-        (bool s, bytes memory r) = address(this).call(
+        (s, r) = address(this).call(

```

```solidity
File : contracts/party/PartyGovernance.sol

431:        uint256 high = snaps.length;
432:        uint256 low = 0;
433:        while (low < high) {
434:            uint256 mid = (low + high) / 2;

```
[431-434](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L431C1-L434C44)

```diff
File : contracts/party/PartyGovernance.sol

        uint256 high = snaps.length;
        uint256 low = 0;
+       uint256 mid; 
        while (low < high) {
-          uint256 mid = (low + high) / 2;
+          mid = (low + high) / 2;

```

```solidity
File : contracts/party/PartyGovernanceNFT.sol

272:     for (uint256 i; i < tokenIds.length; ++i) {
273:          uint256 tokenId = tokenIds[i];
274:          address owner = ownerOf(tokenId);

```
[272-274](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L272C1-L274C46)

```diff
File : contracts/party/PartyGovernanceNFT.sol

+   uint256 tokenId;
+   address owner;
    for (uint256 i; i < tokenIds.length; ++i) {
-       uint256 tokenId = tokenIds[i];
-       address owner = ownerOf(tokenId);
+       tokenId = tokenIds[i];
+       owner = ownerOf(tokenId);

```

```solidity
File : contracts/party/PartyGovernanceNFT.sol

407:      uint16 feeBps_ = feeBps;
408:      for (uint256 i; i < withdrawTokens.length; ++i) {
409:           IERC20 token = withdrawTokens[i];
410:           uint256 amount = withdrawAmounts[i];
411:
412:           // Take fee from amount.
413:           uint256 fee = (amount * feeBps_) / 1e4;
414:
415:           if (fee > 0) {
416:               amount -= fee;
417:
418:               // Transfer fee to fee recipient.
419:               if (address(token) == ETH_ADDRESS) {
420:                   payable(feeRecipient).transferEth(fee);
421:               } else {
422:                   token.compatTransfer(feeRecipient, fee);
423:                 }
424:           }
425:
426:           if (amount > 0) {
427:               uint256 minAmount = minWithdrawAmounts[i];

```
[407-427](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L407C1-L427C63)

```diff
File : contracts/party/PartyGovernanceNFT.sol

        uint16 feeBps_ = feeBps;
+       IERC20 token;
+       uint256 amount;
+       uint256 fee;
+       uint256 minAmount;
        for (uint256 i; i < withdrawTokens.length; ++i) {
-           IERC20 token = withdrawTokens[i];
-           uint256 amount = withdrawAmounts[i];
+           token = withdrawTokens[i];
+           amount = withdrawAmounts[i];

            // Take fee from amount.
            uint256 fee = (amount * feeBps_) / 1e4;

            if (fee > 0) {
                amount -= fee;

                // Transfer fee to fee recipient.
                if (address(token) == ETH_ADDRESS) {
                    payable(feeRecipient).transferEth(fee);
                } else {
                    token.compatTransfer(feeRecipient, fee);
                }
            }

            if (amount > 0) {
                uint256 minAmount = minWithdrawAmounts[i];

```
</details>