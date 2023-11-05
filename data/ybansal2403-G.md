### Gas Optimizations List


| Number | Optimization Details                                                                                      | Instances |
| :----: | :-------------------------------------------------------------------------------------------------------- | :-------: |
| [G-01](#g-01) | Structs can be packed into fewer storage slots | 2 |
| [G-02](#g-02) | Do not calculate constants | 4 |
| [G-03](#g-03) | State variables can be packed into fewer storage slots (without truncation) | 1 |
| [G-04](#g-04) | Use constants instead of type(uintx).max | 13 |
| [G-05](#g-05) | Don't cache state variables or functions only used once | 1 |


<a name='g-01'></a>

## [G-01] Structs can be packed into fewer storage slots

The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to eachother in storage and this will pack the values together into a single 32 byte storage slot (if values combined are <= 32 bytes). If the variables packed together are retrieved together in functions (more likely with structs) we will effectively save ~2000 gas with every subsequent SLOAD for that storage slot. This is due to us incurring a `Gwarmaccess (100 gas)` versus a `Gcoldsload (2100 gas)`.

Total Instances: `2`

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L26C5-L40C6

### `initialContributor` can be tightly packed with `maxContribution`. Saves `2000 GAS` , `1 SLOT`.

```solidity
File: contracts/crowdfund/InitialETHCrowdfund.soll

26:struct InitialETHCrowdfundOptions {
27:        address payable initialContributor;
28:        address initialDelegate;
29:        uint96 minContribution;
30:        uint96 maxContribution;
31:        bool disableContributingForExistingCard;
32:        uint96 minTotalContributions;
33:        uint96 maxTotalContributions;
34:        uint16 exchangeRateBps;
35:        uint16 fundingSplitBps;
36:        address payable fundingSplitRecipient;
37:        uint40 duration;
38:        IGateKeeper gateKeeper;
39:        bytes12 gateKeeperId;
40:    }

```

```diff

diff --git a/contracts/crowdfund/InitialETHCrowdfund.sol b/contracts/crowdfund/InitialETHCrowdfund.sol
index 29ade81..b4c6f27 100644
--- a/contracts/crowdfund/InitialETHCrowdfund.sol
+++ b/contracts/crowdfund/InitialETHCrowdfund.sol
@@ -25,9 +25,9 @@ contract InitialETHCrowdfund is ETHCrowdfundBase {
     // Options to be passed into `initialize()` when the crowdfund is created.
     struct InitialETHCrowdfundOptions {
         address payable initialContributor;
-        address initialDelegate;
-        uint96 minContribution;
         uint96 maxContribution;
+        address initialDelegate;
+        uint96 minContribution;
         bool disableContributingForExistingCard;
         uint96 minTotalContributions;
         uint96 maxTotalContributions;


```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L33C5-L48C6

### `initialContributor` can be tightly packed with `maxContribution`. Saves `2000 GAS` , `1 SLOT`.

```solidity
File: contracts/crowdfund/ETHCrowdfundBase.sol

33:    struct ETHCrowdfundOptions {
34:        Party party;
35:        address payable initialContributor;
36:        address initialDelegate;
37:        uint96 minContribution;
38:        uint96 maxContribution;
39:        bool disableContributingForExistingCard;
40:        uint96 minTotalContributions;
41:        uint96 maxTotalContributions;
42:        uint16 exchangeRateBps;
43:        uint16 fundingSplitBps;
44:        address payable fundingSplitRecipient;
45:        uint40 duration;
46:        IGateKeeper gateKeeper;
47:        bytes12 gateKeeperId;
48:    }


```

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..c452c16 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -33,9 +33,9 @@ contract ETHCrowdfundBase is Implementation {
     struct ETHCrowdfundOptions {
         Party party;
         address payable initialContributor;
-        address initialDelegate;
-        uint96 minContribution;
         uint96 maxContribution;
+        address initialDelegate;
+        uint96 minContribution;
         bool disableContributingForExistingCard;
         uint96 minTotalContributions;
         uint96 maxTotalContributions;

```

<a name='g-02'></a>

## [G-02] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

_The following instances are missed in the automated bot report_

Total Instances: `4`

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L104

```solidity
File: packages/contracts/contracts/BorrowerOperations.sol

104: uint256 private constant _STORAGE_SLOT = uint256(keccak256("ProposalExecutionEngine.Storage"));

```

```diff

diff --git a/contracts/proposals/ProposalExecutionEngine.sol b/contracts/proposals/ProposalExecutionEngine.sol
index 6523a54..ee585b7 100644
--- a/contracts/proposals/ProposalExecutionEngine.sol
+++ b/contracts/proposals/ProposalExecutionEngine.sol
@@ -101,7 +101,8 @@ contract ProposalExecutionEngine is
     IGlobals private immutable _GLOBALS;
     // Storage slot for `Storage`.
     // Use a constant, non-overlapping slot offset for the storage bucket.
-    uint256 private constant _STORAGE_SLOT = uint256(keccak256("ProposalExecutionEngine.Storage"));
+    uint256 private constant _STORAGE_SLOT = 23357286104937499723351598522752547048445797537217589628494188201802841198114;
+    //uint256(keccak256("ProposalExecutionEngine.Storage"));

     // Set immutables.
     constructor(



```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L187

```solidity
File: contracts/party/PartyGovernance.sol

187:  uint96 private constant VETO_VALUE = type(uint96).max;

```

```diff

diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..f0ecc69 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -184,7 +184,8 @@ abstract contract PartyGovernance is
     error TooManyHosts();

     uint256 private constant UINT40_HIGH_BIT = 1 << 39;
-    uint96 private constant VETO_VALUE = type(uint96).max;
+    uint96 private constant VETO_VALUE = 79228162514264337593543950335;
+    //type(uint96).max;

     // The `Globals` contract storing global configuration values. This contract
     // is immutable and it’s address will never change.


```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L40C5-L41C69

```solidity
File: contracts/proposals/ProposalStorage.sol

40:uint256 private constant SHARED_STORAGE_SLOT =
41:        uint256(keccak256("ProposalStorage.SharedProposalStorage"));

```
```diff

diff --git a/contracts/proposals/ProposalStorage.sol b/contracts/proposals/ProposalStorage.sol
index 5a86652..1d79b2d 100644
--- a/contracts/proposals/ProposalStorage.sol
+++ b/contracts/proposals/ProposalStorage.sol
@@ -37,8 +37,8 @@ abstract contract ProposalStorage {
     }

     uint256 internal constant PROPOSAL_FLAG_UNANIMOUS = 0x1;
-    uint256 private constant SHARED_STORAGE_SLOT =
-        uint256(keccak256("ProposalStorage.SharedProposalStorage"));
+    uint256 private constant SHARED_STORAGE_SLOT =106302832050329184827365840188157715900830265941943505593522643729221939540463;
+        //uint256(keccak256("ProposalStorage.SharedProposalStorage"));

     function _initProposalImpl(IProposalExecutionEngine impl, bytes memory initData) internal {
         SharedProposalStorage storage stor = _getSharedProposalStorage();

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L13C4-L14C69

```solidity
File: contracts/proposals/SetSignatureValidatorProposal.sol

13:  uint256 private constant _SET_SIGNATURE_VALIDATOR_PROPOSAL_STORAGE_SLOT =
14:        uint256(keccak256("SetSignatureValidatorProposal.Storage"));

```
```diff

diff --git a/contracts/proposals/SetSignatureValidatorProposal.sol b/contracts/proposals/SetSignatureValidatorProposal.sol
index e17a391..675cc81 100644
--- a/contracts/proposals/SetSignatureValidatorProposal.sol
+++ b/contracts/proposals/SetSignatureValidatorProposal.sol
@@ -10,8 +10,8 @@ abstract contract SetSignatureValidatorProposal {
         mapping(bytes32 => IERC1271) signatureValidators;
     } 
     /// @notice Use a constant, non-overlapping slot offset for the `ZoraProposalStorage` bucket
-    uint256 private constant _SET_SIGNATURE_VALIDATOR_PROPOSAL_STORAGE_SLOT =
-        uint256(keccak256("SetSignatureValidatorProposal.Storage"));
+    uint256 private constant _SET_SIGNATURE_VALIDATOR_PROPOSAL_STORAGE_SLOT =11805265451512502005317972477202665156539486247287160597463508051079946114657;
+        //uint256(keccak256("SetSignatureValidatorProposal.Storage"));

     /// @notice Struct containing data required for this proposal type
     struct SetSignatureValidatorProposalData {


```
<a name='g-03'></a>

## [G-03] State variables can be packed into fewer storage slots (without truncation)

The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to eachother in storage and this will pack the values together into a single 32 byte storage slot (if the values combined are <= 32 bytes). If the variables packed together are retrieved together in functions we will effectively save ~2000 gas with every subsequent SLOAD for that storage slot. This is due to us incurring a `Gwarmaccess (100 gas)` versus a `Gcoldsload (2100 gas)`.

Total Instances: `1`

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L194C5-L210C27

### Pack `numHosts`with `emergencyExecuteDisabled`,`feeBps,`feeRecipient`&`lastRageQuitTimestamp` into one storage slot to save 1 SLOTs (~2000 gas)

```solidity
File: contracts/party/PartyGovernance.sol

194: bool public emergencyExecuteDisabled;
195:    /// @notice Distribution fee bps.
196:    uint16 public feeBps;
197:    /// @notice Distribution fee recipient.
198:    address payable public feeRecipient;
199:    /// @notice The timestamp of the last time `rageQuit()` was called.
200:    uint40 public lastRageQuitTimestamp;
201:    /// @notice The hash of the list of precious NFTs guarded by the party.
202:    bytes32 public preciousListHash;
203:    /// @notice The last proposal ID that was used. 0 means no proposals have been made.
204:    uint256 public lastProposalId;
205:    /// @notice Whether an address is a party host.
206:    mapping(address => bool) public isHost;
207:    /// @notice The last person a voter delegated its voting power to.
208:    mapping(address => address) public delegationsByVoter;
209:    /// @notice Number of hosts for this party
210:    uint8 public numHosts;

```

```dif
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..05e16ac 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -198,6 +198,8 @@ abstract contract PartyGovernance is
     address payable public feeRecipient;
     /// @notice The timestamp of the last time `rageQuit()` was called.
     uint40 public lastRageQuitTimestamp;
+    /// @notice Number of hosts for this party
+    uint8 public numHosts;
     /// @notice The hash of the list of precious NFTs guarded by the party.
     bytes32 public preciousListHash;
     /// @notice The last proposal ID that was used. 0 means no proposals have been made.
@@ -206,8 +208,6 @@ abstract contract PartyGovernance is
     mapping(address => bool) public isHost;
     /// @notice The last person a voter delegated its voting power to.
     mapping(address => address) public delegationsByVoter;
-    /// @notice Number of hosts for this party
-    uint8 public numHosts;
     /// @notice ProposalState by proposal ID.
     mapping(uint256 => ProposalState) private _proposalStateByProposalId;
     /// @notice Snapshots of voting power per user, each sorted by increasing time.


```

<a name='g-04'></a>

## [G-04] Use constants instead of type(uintx).max

It's generally more gas-efficient to use constants instead of type(uintX).max when you need to set the maximum value of an unsigned integer type.

The reason for this is that the type(uintX).max expression involves a computation at runtime, whereas a constant is evaluated at compile-time. This means that using type(uintX).max can result in additional gas costs for each transaction that involves the expression.

By using a constant instead of type(uintX).max, you can avoid these additional gas costs and make your code more efficient.

Here's an example of how you can use a constant instead of type(uintX).max:

```solidity
contract MyContract {
    uint120 constant MAX_VALUE = 2**120 - 1;

    function doSomething(uint120 value) public {
        require(value <= MAX_VALUE, "Value exceeds maximum");

        // Do something
    }
}
```

In the above example, we have a contract with a constant MAX_VALUE that represents the maximum value of a uint120. When the doSomething function is called with a value parameter, it checks whether the value is less than or equal to MAX_VALUE using the <= operator.

By using a constant instead of type(uint120).max, we can make our code more efficient and reduce the gas cost of our contract.

It's important to note that using constants can make your code more readable and maintainable, since the value is defined in one place and can be easily updated if necessary. However, constants should be used with caution and only when their value is known at compile-time.

Total Instances: `13`

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L187C4-L187C59
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L302C9-L302C54
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L359
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L445C9-L445C57
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L520
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L581C9-L581C56
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L655C12-L655C60
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L688
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L833C9-L833C56
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L897C9-L897C56
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L926C12-L926C50
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1083

```solidity
File: contracts/party/PartyGovernance.sol

187: uint96 private constant VETO_VALUE = type(uint96).max;

302: if (govOpts.hosts.length > type(uint8).max) {

359: return getVotingPowerAt(voter, timestamp, type(uint256).max);

445: return high == 0 ? type(uint256).max : high - 1;

520: emit BatchMetadataUpdate(0, type(uint256).max);

581: emit BatchMetadataUpdate(0, type(uint256).max);

655: emit BatchMetadataUpdate(0, type(uint256).max);

688: emit BatchMetadataUpdate(0, type(uint256).max);

833: emit BatchMetadataUpdate(0, type(uint256).max);

897: emit BatchMetadataUpdate(0, type(uint256).max);

926:  if (hintIndex != type(uint256).max) {

1083: if (pv.votes == type(uint96).max) {

```

```diff
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..2af32b3 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -184,7 +184,10 @@ abstract contract PartyGovernance is
     error TooManyHosts();

     uint256 private constant UINT40_HIGH_BIT = 1 << 39;
-    uint96 private constant VETO_VALUE = type(uint96).max;
+    uint96 private constant VETO_VALUE = 79228162514264337593543950335;//type(uint96).max;
+    uint256 private constant MAX_VALUE_UINT256 = 115792089237316195423570985008687907853269984665640564039457584007913129639935;//type(uint256).max;
+    uint8 private constant MAX_VALUE_UINT8 = 255;//type(uint8).max;
+

     // The `Globals` contract storing global configuration values. This contract
     // is immutable and it’s address will never change.
@@ -299,7 +302,7 @@ abstract contract PartyGovernance is
         // Set the precious list.
         _setPreciousList(preciousTokens, preciousTokenIds);
         // Set the party hosts.
-        if (govOpts.hosts.length > type(uint8).max) {
+        if (govOpts.hosts.length > MAX_VALUE_UINT8) {
             revert TooManyHosts();
         }
         for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
@@ -356,7 +359,7 @@ abstract contract PartyGovernance is
         address voter,
         uint40 timestamp
     ) external view returns (uint96 votingPower) {
-        return getVotingPowerAt(voter, timestamp, type(uint256).max);
+        return getVotingPowerAt(voter, timestamp, MAX_VALUE_UINT256);
     }

     /// @notice Get the total voting power of `voter` at a snapshot `snapIndex`, with checks to
@@ -442,7 +445,7 @@ abstract contract PartyGovernance is
         }

         // Return `type(uint256).max` if no valid voting snapshots found.
-        return high == 0 ? type(uint256).max : high - 1;
+        return high == 0 ? MAX_VALUE_UINT256 : high - 1;
     }

     /// @notice Pledge your intrinsic voting power to a new delegate, removing it from
@@ -517,7 +520,7 @@ abstract contract PartyGovernance is
         emit DistributionCreated(tokenType, token, tokenId);
         // Notify third-party platforms that the governance NFT metadata has
         // updated for all tokens.
-        emit BatchMetadataUpdate(0, type(uint256).max);
+        emit BatchMetadataUpdate(0, MAX_VALUE_UINT256);
         // Create a native token distribution.
         address payable feeRecipient_ = feeRecipient;
         uint16 feeBps_ = feeBps;
@@ -578,7 +581,7 @@ abstract contract PartyGovernance is

         // Notify third-party platforms that the governance NFT metadata has
         // updated for all tokens.
-        emit BatchMetadataUpdate(0, type(uint256).max);
+        emit BatchMetadataUpdate(0, MAX_VALUE_UINT256);
     }

     /// @notice Vote to support a proposed proposal.
@@ -652,7 +655,7 @@ abstract contract PartyGovernance is
             emit ProposalPassed(proposalId);
             // Notify third-party platforms that the governance NFT metadata has
             // updated for all tokens.
-            emit BatchMetadataUpdate(0, type(uint256).max);
+            emit BatchMetadataUpdate(0,  MAX_VALUE_UINT256);
         }
         return values.votes;
     }
@@ -685,7 +688,7 @@ abstract contract PartyGovernance is
         emit ProposalVetoed(proposalId, msg.sender);
         // Notify third-party platforms that the governance NFT metadata has
         // updated for all tokens.
-        emit BatchMetadataUpdate(0, type(uint256).max);
+        emit BatchMetadataUpdate(0, MAX_VALUE_UINT256);
     }

     /// @notice Executes a proposal that has passed governance.
@@ -830,7 +833,7 @@ abstract contract PartyGovernance is
         emit ProposalCancelled(proposalId);
         // Notify third-party platforms that the governance NFT metadata has
         // updated for all tokens.
-        emit BatchMetadataUpdate(0, type(uint256).max);
+        emit BatchMetadataUpdate(0,  MAX_VALUE_UINT256);
     }

     /// @notice As the DAO, execute an arbitrary function call from this contract.
@@ -894,7 +897,7 @@ abstract contract PartyGovernance is
         emit ProposalExecuted(proposalId, msg.sender, nextProgressData);
         // Notify third-party platforms that the governance NFT metadata has
         // updated for all tokens.
-        emit BatchMetadataUpdate(0, type(uint256).max);
+        emit BatchMetadataUpdate(0,  MAX_VALUE_UINT256);
         // If the returned progress data is empty, then the proposal completed
         // and it should not be executed again.
         return nextProgressData.length == 0;
@@ -923,7 +926,7 @@ abstract contract PartyGovernance is
             // Hint was wrong, fallback to binary search to find snapshot.
             hintIndex = findVotingPowerSnapshotIndex(voter, timestamp);
             // Check that snapshot was found.
-            if (hintIndex != type(uint256).max) {
+            if (hintIndex !=  MAX_VALUE_UINT256) {
                 return snaps[hintIndex];
             }
         }
@@ -1080,7 +1083,7 @@ abstract contract PartyGovernance is
             return ProposalStatus.Complete;
         }
         // Vetoed.
-        if (pv.votes == type(uint96).max) {
+        if (pv.votes == VETO_VALUE) {
             return ProposalStatus.Defeated;
         }
         uint40 t = uint40(block.timestamp);


```
<a name='g-05'></a>

## [G-05] Don't cache state variables or functions only used once

It's cheaper to access the state variable directly if it is accessed only once. This can save the 3 gas cost of the extra stack allocation.

_The following instances are missed in the automated bot report_

Total Instances: `1`

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L115C9-L115C88

```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol

115: Party party_ = _createParty(partyOpts, customMetadataProvider, customMetadata);

```
```diff
diff --git a/contracts/crowdfund/InitialETHCrowdfund.sol b/contracts/crowdfund/InitialETHCrowdfund.sol
index 29ade81..7f16463 100644
--- a/contracts/crowdfund/InitialETHCrowdfund.sol
+++ b/contracts/crowdfund/InitialETHCrowdfund.sol
@@ -112,12 +112,12 @@ contract InitialETHCrowdfund is ETHCrowdfundBase {
         bytes memory customMetadata
     ) external payable onlyInitialize {
         // Create party the initial crowdfund will be for.
-        Party party_ = _createParty(partyOpts, customMetadataProvider, customMetadata);
+

         // Initialize the crowdfund.
         _initialize(
             ETHCrowdfundOptions({
-                party: party_,
+                party:_createParty(partyOpts, customMetadataProvider, customMetadata),
                 initialContributor: crowdfundOpts.initialContributor,
                 initialDelegate: crowdfundOpts.initialDelegate,
                 minContribution: crowdfundOpts.minContribution,


```
