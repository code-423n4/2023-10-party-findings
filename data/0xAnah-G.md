# PARTY PROTOCOL GAS OPTIMISATIONS

## INTRODUCTION
Please be aware that some code snippets may be shortened to conserve space, and certain code snippets may include @audit tags in comments to facilitate issue explanations."

Emphasizing the significance of our recommendations, we strive to improve the code's efficiency while maintaining its readability. We recognize the importance of code that is easily maintainable and understandable for both developers and auditors.






## [G-01] Refactor function to avoid performing same calculation on every loop iteration.
### 1 Instance

1. #### Refactor `_createParty` function to avoid performing the `authoritiesLength - 1` calculation on every iteration of the loop.

Rather than having to perform `authoritiesLength - 1` calculation on every iteration of the loop we could memoize (i.e cache result of the calculation) the calculation there by saving the upto `6` gas unit per iteration of the loop also we would avoid having to re-perform the calculation on [Line 382](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L382). The code should be refactored as shown in the diff below:

- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379
```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

372:    function _createParty(
373:        ETHPartyOptions memory opts,
374:        MetadataProvider customMetadataProvider,
375:        bytes memory customMetadata
376:    ) private returns (Party) {
377:        uint256 authoritiesLength = opts.authorities.length + 1;
378:        address[] memory authorities = new address[](authoritiesLength);
379:        for (uint i = 0; i < authoritiesLength - 1; ++i) {//@audit memoize authoritiesLength - 1 calculation
380:            authorities[i] = opts.authorities[i];
381:        }
382:        authorities[authoritiesLength - 1] = address(this);
.
.
.
435:    }
```

```diff
diff --git a/contracts/crowdfund/InitialETHCrowdfund.sol b/contracts/crowdfund/InitialETHCrowdfund.sol
index 29ade81..d67dffc 100644
--- a/contracts/crowdfund/InitialETHCrowdfund.sol
+++ b/contracts/crowdfund/InitialETHCrowdfund.sol
@@ -374,12 +374,12 @@ contract InitialETHCrowdfund is ETHCrowdfundBase {
         MetadataProvider customMetadataProvider,
         bytes memory customMetadata
     ) private returns (Party) {
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

         if (address(customMetadataProvider) == address(0)) {
             return
```
```
Estimated gas saved: 6 gas per iteration
```





## [G-02] Use the existing Local variable/global variable when equal to a state variable to avoid reading from state

### 3 Instances

1. #### Use stack variable `maxTotalContributions_` in place of `maxTotalContributions` on [line 238](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L228) to avoid reading from state.
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L228-#L238

In the `_processContribution`, function on [Line 228](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L238) the value of state variable `maxTotalContributions` was copied into stack variable `maxTotalContributions_`. We would avoid 1 `SLOAD` (Gwarmaccess) `100` gas units if we refactor the statement on [line 238](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L238) such that the stack variable `maxTotalContributions_` is used in place of the state variable `maxTotalContributions` in the calculation. The diff below shows the refactored code:

```solidity
file: contracts/crowdfund/ETHCrowdfundBase.sol

196:    function _processContribution(
197:        address payable contributor,
198:        address delegate,
199:        uint96 amount
200:    ) internal returns (uint96 votingPower) {
201:        address oldDelegate = delegationsByContributor[contributor];
202:        if (msg.sender == contributor || oldDelegate == address(0)) {
.
.
.
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
238:            uint96 refundAmount = newTotalContributions - maxTotalContributions; //@audit could use maxTotalContributions_ instead.
.
.
.            
273:    }
```

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..7a717ac 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -235,7 +235,7 @@ contract ETHCrowdfundBase is Implementation {
             _finalize(maxTotalContributions_);

             // Refund excess contribution.
-            uint96 refundAmount = newTotalContributions - maxTotalContributions;
+            uint96 refundAmount = newTotalContributions - maxTotalContributions_;
             if (refundAmount > 0) {
                 amount -= refundAmount;
                 payable(msg.sender).transferEth(refundAmount);
```
```
Estimated gas saved: 97 gas units
```

2. #### Use stack variable `mintedVotingPower_` instead of reading `mintedVotingPower` from state on [line 189](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L189).
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L189

In the `mint` function, on [Line 172](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L172) the value of state variable `mintedVotingPower` was copied into stack variable `mintedVotingPower_`. We would avoid 1 `SLOAD` (Gwarmaccess) `100` gas units if we refactor the statement on [line 189](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L189) such that the stack variable `mintedVotingPower_` is used rather than having to read `mintedVotingPower` from state in the calculation. The diff below shows the refactored code:

```solidity
file: contracts/party/PartyGovernance.sol

166:    function mint(
167:        address owner,
168:        uint256 votingPower,
169:        address delegate
170:    ) external returns (uint256 tokenId) {
171:        _assertAuthority();
172:        uint96 mintedVotingPower_ = mintedVotingPower;
173:        uint96 totalVotingPower = _getSharedProposalStorage().governanceValues.totalVotingPower;
174:
175:        // Cap voting power to remaining unminted voting power supply.
176:        uint96 votingPower_ = votingPower.safeCastUint256ToUint96();
177:        // Allow minting past total voting power if minting party cards for
178:        // initial crowdfund when there is no total voting power.
179:        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower_) {
180:            unchecked {
181:                votingPower_ = totalVotingPower - mintedVotingPower_;
182:            }
183:        }
184:
185:        // Update state.
186:        unchecked {
187:            tokenId = ++tokenCount;
188:        }
189:        mintedVotingPower += votingPower_;
.
.
.
202:    }
```

```diff
diff --git a/contracts/party/PartyGovernanceNFT.sol b/contracts/party/PartyGovernanceNFT.sol
index 997b00c..5580c8b 100644
--- a/contracts/party/PartyGovernanceNFT.sol
+++ b/contracts/party/PartyGovernanceNFT.sol
@@ -186,7 +186,7 @@ contract PartyGovernanceNFT is PartyGovernance, ERC721, IERC2981 {
         unchecked {
             tokenId = ++tokenCount;
         }
-        mintedVotingPower += votingPower_;
+        mintedVotingPower = mintedVotingPower_ + votingPower_;
         votingPowerByTokenId[tokenId] = votingPower_;

         emit PartyCardIntrinsicVotingPowerSet(tokenId, votingPower_);
```
```
Estimated gas saved: 97 gas un
```

3. #### Use stack variable `mintedVotingPower_` instead of reading `mintedVotingPower` from state on [line 223](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L223).
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L210&&#L223

In the `increaseVotingPower` function, on [Line 210](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L210) the value of state variable `mintedVotingPower` was copied into stack variable `mintedVotingPower_`. We would avoid 1 `SLOAD` (Gwarmaccess) `100` gas units if we refactor the statement on [line 223](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L223) such that the stack variable `mintedVotingPower_` is used rather than having to read `mintedVotingPower` from state in the calculation. The diff below shows the refactored code:

```solidity
file: contracts/party/PartyGovernanceNFT.sol

208:    function increaseVotingPower(uint256 tokenId, uint96 votingPower) external {
209:        _assertAuthority();
210:        uint96 mintedVotingPower_ = mintedVotingPower;
211:        uint96 totalVotingPower = _getSharedProposalStorage().governanceValues.totalVotingPower;
212:
213:        // Cap voting power to remaining unminted voting power supply. Allow
214:        // minting past total voting power if minting party cards for initial
215:        // crowdfund when there is no total voting power.
216:        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower) {
217:            unchecked {
218:                votingPower = totalVotingPower - mintedVotingPower_;
219:            }
220:        }
221:
222:        // Update state.
223:        mintedVotingPower += votingPower;
224:        uint256 newIntrinsicVotingPower = votingPowerByTokenId[tokenId] + votingPower;
225:        votingPowerByTokenId[tokenId] = newIntrinsicVotingPower;
226:
227:        emit PartyCardIntrinsicVotingPowerSet(tokenId, newIntrinsicVotingPower);
228:
229:        _adjustVotingPower(ownerOf(tokenId), votingPower.safeCastUint96ToInt192(), address(0));
230:    }
```

```diff
diff --git a/contracts/party/PartyGovernanceNFT.sol b/contracts/party/PartyGovernanceNFT.sol
index 997b00c..2e8ba2b 100644
--- a/contracts/party/PartyGovernanceNFT.sol
+++ b/contracts/party/PartyGovernanceNFT.sol
@@ -220,7 +220,7 @@ contract PartyGovernanceNFT is PartyGovernance, ERC721, IERC2981 {
         }

         // Update state.
-        mintedVotingPower += votingPower;
+        mintedVotingPower = mintedVotingPower_ + votingPower;
         uint256 newIntrinsicVotingPower = votingPowerByTokenId[tokenId] + votingPower;
         votingPowerByTokenId[tokenId] = newIntrinsicVotingPower;
```
```
Estimated gas saved: 97 gas units
```




## [G-03] Refactor function such that all checks are performed before assigning values to state variables

### 1 Instance
1. #### Refactor `_initialize` function of `ETHCrowdfundBase` contract such that all the checks on the arguments are performed before assigning the values to state variables.
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L140-#L172

The `_initialize` function of `ETHCrowdfundBase` contract should be refactored such that all the checks on the arguments are done before assigning the values to the state variables because for example in a scenario such that the check on [line 148 - Line 150](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L148-#L150) fails the function would revert but it would have assigned values to `minContribution` and `maxContribution` state variables costing 2 `SSTORE` `40000` gas units if it is the first time the storage slot is being set or `5800` if these slots have been previously set. But if all the checks are performed first before assigning any value to any of state variables and a check fails the function would revert without performing consuming huge amount of gas thereby making the function better gas efficient. The diff below shows how the code could be refcatored:  

```solidity
file: contracts/crowdfund/ETHCrowdfundBase.sol

140:    function _initialize(ETHCrowdfundOptions memory opts) internal {
141:        // Set the minimum and maximum contribution amounts.
142:        if (opts.minContribution > opts.maxContribution) {
143:            revert MinGreaterThanMaxError(opts.minContribution, opts.maxContribution);
144:        }
145:        minContribution = opts.minContribution;
146:        maxContribution = opts.maxContribution;
147:        // Set the min total contributions.
148:        if (opts.minTotalContributions > opts.maxTotalContributions) {
149:            revert MinGreaterThanMaxError(opts.minTotalContributions, opts.maxTotalContributions);
150:        }
151:        minTotalContributions = opts.minTotalContributions;
152:        // Set the max total contributions.
153:        if (opts.maxTotalContributions == 0) {
154:            // Prevent this because when `maxTotalContributions` is 0 the
155:            // crowdfund is invalid in `getCrowdfundLifecycle()` meaning it has
156:            // never been initialized.
157:            revert MaxTotalContributionsCannotBeZeroError(opts.maxTotalContributions);
158:        }
159:        maxTotalContributions = opts.maxTotalContributions;
160:        // Set the party crowdfund is for.
161:        party = opts.party;
162:        // Set the crowdfund start and end timestamps.
163:        expiry = uint40(block.timestamp + opts.duration);
164:        // Set the exchange rate.
165:        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
166:        exchangeRateBps = opts.exchangeRateBps;
167:        // Set the funding split and its recipient.
168:        fundingSplitBps = opts.fundingSplitBps;
169:        fundingSplitRecipient = opts.fundingSplitRecipient;
170:        // Set whether to disable contributing for existing card.
171:        disableContributingForExistingCard = opts.disableContributingForExistingCard;
172:    }
```

```diff
index db0e78d..99dd871 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -138,31 +138,35 @@ contract ETHCrowdfundBase is Implementation {
     // Initialize storage for proxy contracts, credit initial contribution (if
     // any), and setup gatekeeper.
     function _initialize(ETHCrowdfundOptions memory opts) internal {
-        // Set the minimum and maximum contribution amounts.
+
         if (opts.minContribution > opts.maxContribution) {
             revert MinGreaterThanMaxError(opts.minContribution, opts.maxContribution);
         }
-        minContribution = opts.minContribution;
-        maxContribution = opts.maxContribution;
-        // Set the min total contributions.
+
         if (opts.minTotalContributions > opts.maxTotalContributions) {
             revert MinGreaterThanMaxError(opts.minTotalContributions, opts.maxTotalContributions);
         }
-        minTotalContributions = opts.minTotalContributions;
-        // Set the max total contributions.
+        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
+
         if (opts.maxTotalContributions == 0) {
             // Prevent this because when `maxTotalContributions` is 0 the
             // crowdfund is invalid in `getCrowdfundLifecycle()` meaning it has
             // never been initialized.
             revert MaxTotalContributionsCannotBeZeroError(opts.maxTotalContributions);
         }
+
+        // Set the minimum and maximum contribution amounts.
+        minContribution = opts.minContribution;
+        maxContribution = opts.maxContribution;
+        // Set the min total contributions.
+        minTotalContributions = opts.minTotalContributions;
+        // Set the max total contributions.
         maxTotalContributions = opts.maxTotalContributions;
         // Set the party crowdfund is for.
         party = opts.party;
         // Set the crowdfund start and end timestamps.
         expiry = uint40(block.timestamp + opts.duration);
         // Set the exchange rate.
-        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
         exchangeRateBps = opts.exchangeRateBps;
         // Set the funding split and its recipient.
         fundingSplitBps = opts.fundingSplitBps;
```
```
Estimated gas saved: The gas saved would depend on the scenario that plays out.
```

## [G-04] Redundant state variable getters
Getters for public state variables are automatically generated by the solidity compiler so there is no need to code them manually as this increases deployment cost.
#### Please note that this instances was not included in the bots report.

### 1 Instance
1. #### Make `votingPowerByTokenId` mapping variable `private` or `internal`.
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L57

The solidity compiler would automatically create a getter function for the `votingPowerByTokenId` mapping above since it is declared as a `public` variable but the `getDistributionShareOf()` function on [Line 146 - Line 148](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L146-#L148) could represent a getter function for the same variable thereby making it two getter functions for the same variable in the contract. We could rectify this issue by making the `votingPowerByTokenId` variable private or internal (if the variable is to be inherited). The diff below shows how the code could be refactored:

```solidity
file: contracts/party/PartyGovernanceNFT.sol

57:    mapping(uint256 => uint256) public votingPowerByTokenId;

146:    function getDistributionShareOf(uint256 tokenId) external view returns (uint256) {
147:        return votingPowerByTokenId[tokenId];
148:    }
```

```diff
diff --git a/contracts/party/PartyGovernanceNFT.sol b/contracts/party/PartyGovernanceNFT.sol
index 997b00c..9807766 100644
--- a/contracts/party/PartyGovernanceNFT.sol
+++ b/contracts/party/PartyGovernanceNFT.sol
@@ -54,7 +54,7 @@ contract PartyGovernanceNFT is PartyGovernance, ERC721, IERC2981 {
     ///         initialization.
     uint40 public rageQuitTimestamp;
     /// @notice The voting power of `tokenId`.
-    mapping(uint256 => uint256) public votingPowerByTokenId;
+    mapping(uint256 => uint256) internal votingPowerByTokenId;
     /// @notice Address with authority to mint cards and update voting power for the party.
     mapping(address => bool) public isAuthority;
```




## [G-05] Structs can be packed to fit in fewer storage slots
In solidity we can pack the members of a struct such that more than one member could share a storage slot if their combined size is less than or equal to 32 bytes. The members struct below could be re-arranged such we multiple members share the same storage slot thereby reducing the number of storage slots to be used by the struct. Each slot reduced can avoid an extra Gsset (20000 gas) for the first setting of the struct and subsequent reads as well as writes would have smaller gas costs.
For more read [Solidity Documentation](https://docs.soliditylang.org/en/v0.8.23/internals/layout_in_storage.html)

### 2 Instances
1. #### The `InitialETHCrowdfundOptions` struct can be packed into fewer storage slots.
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L26-#L40

The members `InitialETHCrowdfundOptions` struct can be re-arranged such that we reduce the number of storage slot required for the `InitialETHCrowdfundOptions` struct from 6 to 5 (i.e we save 1 storage slot). We can move member `uint96 maxTotalContributions` from its position to the top of the struct so that it shares slot with `address payable initialContributor`. In Implementing this we would avoid an extra Gsset (20000 gas) for the first setting of the struct and subsequent reads as well as writes would have smaller gas costs. The diff below show how the struct should be refactored:

```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

26:    struct InitialETHCrowdfundOptions {
27:        address payable initialContributor;        //(20 bytes)  
28:        address initialDelegate;                   //(20 bytes)
29:        uint96 minContribution;                    //(12 bytes)
30:        uint96 maxContribution;                    //(12 bytes)
31:        bool disableContributingForExistingCard;   //(1 byte)
32:        uint96 minTotalContributions;              //(12 bytes)
33:        uint96 maxTotalContributions;              //(12 bytes)
34:        uint16 exchangeRateBps;                    //(2 bytes)
35:        uint16 fundingSplitBps;                    //(2 bytes)
36:        address payable fundingSplitRecipient;     //(20 bytes)
37:        uint40 duration;                           //(5 bytes)
38:        IGateKeeper gateKeeper;                    //(20 bytes)
39:        bytes12 gateKeeperId;                      //(12 bytes)
40:    }
```

```diff
diff --git a/contracts/crowdfund/InitialETHCrowdfund.sol b/contracts/crowdfund/InitialETHCrowdfund.sol
index 29ade81..bea9992 100644
--- a/contracts/crowdfund/InitialETHCrowdfund.sol
+++ b/contracts/crowdfund/InitialETHCrowdfund.sol
@@ -24,13 +24,13 @@ contract InitialETHCrowdfund is ETHCrowdfundBase {

     // Options to be passed into `initialize()` when the crowdfund is created.
     struct InitialETHCrowdfundOptions {
+        uint96 maxTotalContributions;
         address payable initialContributor;
         address initialDelegate;
         uint96 minContribution;
         uint96 maxContribution;
         bool disableContributingForExistingCard;
         uint96 minTotalContributions;
-        uint96 maxTotalContributions;
         uint16 exchangeRateBps;
         uint16 fundingSplitBps;
         address payable fundingSplitRecipient;
         uint40 duration;
         IGateKeeper gateKeeper;
         bytes12 gateKeeperId; 
```
```
Estimated gas saved: 20000
```

2. #### The `ETHCrowdfundOptions` struct can be packed into fewer storage slots.
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L33-#L48

The members `ETHCrowdfundOptions` struct can be re-arranged such that we reduce the number of storage slot required for the `ETHCrowdfundOptions` struct from 7 to 6 (i.e we save 1 storage slot). We can move member `uint96 maxContribution` from its position to the top of the struct so that it shares slot with `Party party` also we would pair member `uint96 minTotalContributions` with member `address payable initialContributor` so they also share the same storage slot. In Implementing this we would avoid an extra Gsset (20000 gas) for the first setting of the struct and subsequent reads as well as writes would have smaller gas costs. The diff below show how the struct should be refactored:

```solidity
file: contracts/crowdfund/ETHCrowdfundBase.sol

33:    struct ETHCrowdfundOptions {
34:        Party party;                             //(20 bytes)
35:        address payable initialContributor;      //(20 bytes)
36:        address initialDelegate;                 //(20 bytes)
37:        uint96 minContribution;                  //(12 bytes)
38:        uint96 maxContribution;                  //(12 bytes)
39:        bool disableContributingForExistingCard; //(1 byte)
40:        uint96 minTotalContributions;            //(12 bytes)
41:        uint96 maxTotalContributions;            //(12 bytes)
42:        uint16 exchangeRateBps;                  //(2 bytes)
43:        uint16 fundingSplitBps;                  //(2 bytes)
44:        address payable fundingSplitRecipient;   //(20 bytes)
45:        uint40 duration;                         //(5 bytes)
46:        IGateKeeper gateKeeper;                  //(20 bytes)
47:        bytes12 gateKeeperId;                    //(12 bytes)
48:    }
```

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..c62c3b6 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -32,12 +32,12 @@ contract ETHCrowdfundBase is Implementation {
     // Options to be passed into `initialize()` when the crowdfund is created.
     struct ETHCrowdfundOptions {
         Party party;
+        uint96 maxContribution;
         address payable initialContributor;
+        uint96 minTotalContributions;
         address initialDelegate;
         uint96 minContribution;
-        uint96 maxContribution;
         bool disableContributingForExistingCard;
-        uint96 minTotalContributions;
         uint96 maxTotalContributions;
         uint16 exchangeRateBps;
         uint16 fundingSplitBps;
         address payable fundingSplitRecipient;
         uint40 duration;
         IGateKeeper gateKeeper;
         bytes12 gateKeeperId;
     }
```
```
Estimated gas saved: 20000 gas units
```




## [G-06] Add unchecked blocks for subtractions where the operands cannot underflow
There are some checks to avoid an underflow, so it's safe to use unchecked to have some gas savings.

### 4 Instances
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L238

From the `_processContribution()` function below the if statement on [Line 229](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L229) ensures that the value of `newTotalContributions` is greater or equals the value of `maxTotalContributions` before the calculation can occur thereby making it impossible that the calculation would underflow. Therefore we can save up to `40` gas units if we do this calculation in an `unchecked{}` block as this would save some gas from the unnecessary internal over/underflow checks. The diff below shows how the code could be refactored:

```solidity
file: contracts/crowdfund/ETHCrowdfundBase.sol

196:    function _processContribution(
197:        address payable contributor,
198:        address delegate,
199:        uint96 amount
200:    ) internal returns (uint96 votingPower) {
201:        address oldDelegate = delegationsByContributor[contributor];
.
.
.
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
238:            uint96 refundAmount = newTotalContributions - maxTotalContributions;//@audit can be unchecked
239:            if (refundAmount > 0) {
240:                amount -= refundAmount;
241:                payable(msg.sender).transferEth(refundAmount);
242:            }
243:        } else {
244:            totalContributions = newTotalContributions;
245:        }
.
.
.
273:    }
```
```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..1b671c3 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -235,7 +235,11 @@ contract ETHCrowdfundBase is Implementation {
             _finalize(maxTotalContributions_);

             // Refund excess contribution.
-            uint96 refundAmount = newTotalContributions - maxTotalContributions;
+            uint96 refundAmount;
+            unchecked {
+                refundAmount = newTotalContributions - maxTotalContributions;
+            }
+
             if (refundAmount > 0) {
                 amount -= refundAmount;
                 payable(msg.sender).transferEth(refundAmount);
```
```
Estimated gas saved: 40 gas units
```


- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L445

The ternary statement on [line 445](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L445) can be unchecked the since the calculation `high - 1` would only be computed if the value of `high` is greater than 0 (high is a uint256 variable so it cannot have a negative value).so in implementing this change we would save up to `40` gas units as compiler would no longer include checks for underflow. The diff below shows how the code should be refactored:

```solidity
file: contracts/party/PartyGovernance.sol

423:    function findVotingPowerSnapshotIndex(
424:        address voter,
425:        uint40 timestamp
426:    ) public view returns (uint256 index) {
427:        VotingPowerSnapshot[] storage snaps = _votingPowerSnapshotsByVoter[voter];
428:
429:        // Derived from Open Zeppelin binary search
430:        // ref: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Checkpoints.sol#L39
431:        uint256 high = snaps.length;
432:        uint256 low = 0;
433:        while (low < high) {
434:            uint256 mid = (low + high) / 2;
435:            if (snaps[mid].timestamp > timestamp) {
436:                // Entry is too recent.
437:                high = mid;
438:            } else {
439:                // Entry is older. This is our best guess for now.
440:                low = mid + 1;
441:            }
442:        }
443:
444:        // Return `type(uint256).max` if no valid voting snapshots found.
445:        return high == 0 ? type(uint256).max : high - 1;    //@audit high - 1 can be unchecked
446:    }
```

```diff
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..2ab8f1c 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -442,7 +442,10 @@ abstract contract PartyGovernance is
         }

         // Return `type(uint256).max` if no valid voting snapshots found.
-        return high == 0 ? type(uint256).max : high - 1;
+        unchecked {
+            return high == 0 ? type(uint256).max : high - 1;
+        }
+
```
```
Estimated gas saved: 40 gas units
```


- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1034

In the `_insertVotingPowerSnapshot()` function below the if statement on [Line 1033](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1033) ensures that the value of variable `n` is greater than 0 (since n is of type uint256 it cannot be negative) therefore we can avoid the checks underflow checks that would be performed in calculations of `voterSnaps[n - 1]` thereby saving up to `40` gas units. The diff below shows how the code could be refactored:

```solidity
file: contracts/party/PartyGovernance.sol

1021:    function _insertVotingPowerSnapshot(address voter, VotingPowerSnapshot memory snap) private {
1022:        emit PartyVotingSnapshotCreated(
1023:            voter,
1024:            snap.timestamp,
1025:            snap.delegatedVotingPower,
1026:            snap.intrinsicVotingPower,
1027:            snap.isDelegated
1028:        );
1029:
1030:        VotingPowerSnapshot[] storage voterSnaps = _votingPowerSnapshotsByVoter[voter];
1031:        uint256 n = voterSnaps.length;
1032:        // If same timestamp as last entry, overwrite the last snapshot, otherwise append.
1033:        if (n != 0) {
1034:            VotingPowerSnapshot memory lastSnap = voterSnaps[n - 1];
1035:            if (lastSnap.timestamp == snap.timestamp) {
1036:                voterSnaps[n - 1] = snap;
1037:                return;
1038:            }
1039:        }
1040:        voterSnaps.push(snap);
1041:    }
```
```diff
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..c79ad9f 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -1031,11 +1031,14 @@ abstract contract PartyGovernance is
         uint256 n = voterSnaps.length;
         // If same timestamp as last entry, overwrite the last snapshot, otherwise append.
         if (n != 0) {
-            VotingPowerSnapshot memory lastSnap = voterSnaps[n - 1];
-            if (lastSnap.timestamp == snap.timestamp) {
-                voterSnaps[n - 1] = snap;
-                return;
+            unchecked{
+                VotingPowerSnapshot memory lastSnap = voterSnaps[n - 1];
+                if (lastSnap.timestamp == snap.timestamp) {
+                    voterSnaps[n - 1] = snap;
+                    return;
+                }
             }
```
```
Estimated gas saved: 40 gas units
```


- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1049

In the `_getLastVotingPowerSnapshotForVoter()` function below the if statement on [Line 1048](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1048) ensures that the value of variable `n` is greater than 0 (since n is of type uint256 it cannot be negative) therefore we can avoid the checks underflow checks that would be performed in calculations of `voterSnaps[n - 1]` thereby saving up to `40` gas units. The diff below shows how the code could be refactored:

```solidity
file: contracts/party/PartyGovernance.sol

1043:    function _getLastVotingPowerSnapshotForVoter(
1044:        address voter
1045:    ) private view returns (VotingPowerSnapshot memory snap) {
1046:        VotingPowerSnapshot[] storage voterSnaps = _votingPowerSnapshotsByVoter[voter];
1047:        uint256 n = voterSnaps.length;
1048:        if (n != 0) {
1049:            snap = voterSnaps[n - 1];
1050:        }
1051:    }
```

```diff
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..d2aacaa 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -1046,7 +1046,10 @@ abstract contract PartyGovernance is
         VotingPowerSnapshot[] storage voterSnaps = _votingPowerSnapshotsByVoter[voter];
         uint256 n = voterSnaps.length;
         if (n != 0) {
-            snap = voterSnaps[n - 1];
+            unchecked {
+                snap = voterSnaps[n - 1];
+            }
+
         }
     }
```
```
Estimated gas saved: 40 gas units
```




## [G-07] Require() or revert() statements that check input arguments should be at the top of the function so that the function can fail early and cheaply.
Checks that involve input arguments or constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting alot of gas in a function that may ultimately revert in the unhappy case.
#### Please note that this instances was not included in the bots report.

### 1 Instance
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L223-#L225

The if revert statement of [Line 223 - Line225](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L223-#L225) should be moved to the top of the function so that in scenarios in which the value of the `amount` argument is greater than the value of `maxContribution` the function can revert early without having execute the gas consuming statements between [Line 201 and Line 219](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L201-#L219) which includes but not limited to haing to read state and calculate keccak in getting the value from `delegationsByContributor[contributor]` mapping on [line 201](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L201), emmiting the `contributed()` event on [line 210](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L210), invoking the `getCrowdfundLifecycle()` function on [line 216](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L216). The code should be refactored as shown in the diff below: 

```solidity
file: contracts/crowdfund/ETHCrowdfundBase.sol

196:    function _processContribution(
197:        address payable contributor,
198:        address delegate,
199:        uint96 amount
200:    ) internal returns (uint96 votingPower) {
201:        address oldDelegate = delegationsByContributor[contributor];
202:        if (msg.sender == contributor || oldDelegate == address(0)) {
203:            // Update delegate.
204:            delegationsByContributor[contributor] = delegate;
205:        } else {
206:            // Prevent changing another's delegate if already delegated.
207:            delegate = oldDelegate;
208:        }
209:
210:        emit Contributed(msg.sender, contributor, amount, delegate);
211:
212:        // OK to contribute with zero just to update delegate.
213:        if (amount == 0) return 0;
214:
215:        // Only allow contributions while the crowdfund is active.
216:        CrowdfundLifecycle lc = getCrowdfundLifecycle();
217:        if (lc != CrowdfundLifecycle.Active) {
218:            revert WrongLifecycleError(lc);
219:        }
220:
221:        // Check that the contribution amount is at or below the maximum.
222:        uint96 maxContribution_ = maxContribution;
223:        if (amount > maxContribution_) {        //@audit move check to top of function
224:            revert AboveMaximumContributionsError(amount, maxContribution_);
225:        }
.
.
.
273:    }
```

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol            
index db0e78d..31ab27a 100644                                                                               
--- a/contracts/crowdfund/ETHCrowdfundBase.sol                                                              
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol                                                              
@@ -198,6 +198,12 @@ contract ETHCrowdfundBase is Implementation {                                          
         address delegate,                                                                                  
         uint96 amount                                                                                      
     ) internal returns (uint96 votingPower) {                                                              
+        // Check that the contribution amount is at or below the maximum.                                  
+        uint96 maxContribution_ = maxContribution;                                                         
+        if (amount > maxContribution_) {                                                                   
+            revert AboveMaximumContributionsError(amount, maxContribution_);                               
+        }                                                                                                  
+                                                                                                           
         address oldDelegate = delegationsByContributor[contributor];                                       
         if (msg.sender == contributor || oldDelegate == address(0)) {                                      
             // Update delegate.                                                                            
@@ -218,11 +224,7 @@ contract ETHCrowdfundBase is Implementation {                                          
             revert WrongLifecycleError(lc);                                                                
         }                                                                                                  
                                                                                                            
-        // Check that the contribution amount is at or below the maximum.                                  
-        uint96 maxContribution_ = maxContribution;                                                         
-        if (amount > maxContribution_) {                                                                   
-            revert AboveMaximumContributionsError(amount, maxContribution_);                               
-        }                                                                                                  
+                                                                                                           
                                                                                                            
         uint96 newTotalContributions = totalContributions + amount;                                        
         uint96 maxTotalContributions_ = maxTotalContributions;                                             
```




## [G-08] Refactor Event to avoid emmiting constant.
Constants can always be trivially calculated therefore we would save loading data into memory (potentially avoiding memory expansion costs) and Glogdata (8 gas) * bytes emitted.


### 1 Instance

The `BatchMetadataUpdate()` event should be refactored such that it does not have to emit constant values `0` and `type(uint256).max` implementing this would reduce the gas cost of functions which `BatchMetadataUpdate()` was emitted by `512` gas units (Glogdata `8` gas *  (32 + 32) for both constants ). The emit statements are listed below:
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L520
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L581
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L655
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L688
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L833
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L897




## [G-09] Use custom errors instead of require/assert
Consider the use of a custom error, as it leads to a cheaper deploy cost and run time cost. The run time cost is only relevant when the revert condition is met.

### Instances 
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L313
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L314
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L533
- https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L314




## CONCLUSION
As you embark on incorporating the recommended optimizations, we want to emphasize the utmost importance of proceeding with vigilance and dedicating thorough efforts to comprehensive testing. It is of paramount significance to ensure that the proposed alterations do not inadvertently introduce fresh vulnerabilities, while also successfully achieving the anticipated enhancements in performance.

We strongly advise conducting a meticulous and exhaustive evaluation of the modifications made to the codebase. This rigorous scrutiny and exhaustive assessment will play a pivotal role in affirming both the security and efficacy of the refactored code. Your careful attention to detail, coupled with the implementation of a robust testing framework, will provide the necessary assurance that the refined code aligns with your security objectives and effectively fulfills the intended performance optimizations.