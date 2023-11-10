# Table Of Contents
- [Table Of Contents](#table-of-contents)
  - [Use the already cached variable instead of reading from storage(Save 1 SLOAD: 100 Gas)](#use-the-already-cached-variable-instead-of-reading-from-storagesave-1-sload-100-gas)
  - [Modifier execution order can help save an entire SLOAD + an external call(Save 2100+ Gas)](#modifier-execution-order-can-help-save-an-entire-sload--an-external-callsave-2100-gas)
  - [Optimize this code by Only casting if indeed `msg.value > 0`(Saves gas for both \>0 and == 0 scenarios)](#optimize-this-code-by-only-casting-if-indeed-msgvalue--0saves-gas-for-both-0-and--0-scenarios)
  - [PartyGovernanceNFT.sol.setRageQuit(): We can optimize this function](#partygovernancenftsolsetragequit-we-can-optimize-this-function)
  - [Avoid making any SSTOREs before validating all parameters(Save some SSTORES in case of reverts)](#avoid-making-any-sstores-before-validating-all-parameterssave-some-sstores-in-case-of-reverts)
  - [Due to how short circuit works, we can save an entire SLOAD by performing sloads only when needed(Save 2100 Gas)](#due-to-how-short-circuit-works-we-can-save-an-entire-sload-by-performing-sloads-only-when-neededsave-2100-gas)
  - [Avoid an SLOAD by taking advantage of short circuit(1 SLOAD: 2100 Gas)](#avoid-an-sload-by-taking-advantage-of-short-circuit1-sload-2100-gas)
  - [Avoid making one SLOAD by utilizing the rules of short circuit(Save 2100 Gas)](#avoid-making-one-sload-by-utilizing-the-rules-of-short-circuitsave-2100-gas)
  - [Prioritize making cheaper checks  first to reduce gas consumption in revert cases](#prioritize-making-cheaper-checks--first-to-reduce-gas-consumption-in-revert-cases)
  - [Validate all parameters first before performing any other operations if there's no side effects](#validate-all-parameters-first-before-performing-any-other-operations-if-theres-no-side-effects)
  - [Uncheck arithmetic operations that cannot underflow/overflow(The bot missed these)](#uncheck-arithmetic-operations-that-cannot-underflowoverflowthe-bot-missed-these)

## Use the already cached variable instead of reading from storage(Save 1 SLOAD: 100 Gas)

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L228-L243
```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
228:        uint96 maxTotalContributions_ = maxTotalContributions;
229:        if (newTotalContributions >= maxTotalContributions_) {
230:            totalContributions = maxTotalContributions_;

238:            uint96 refundAmount = newTotalContributions - maxTotalContributions;
239:            if (refundAmount > 0) {
240:                amount -= refundAmount;
241:                payable(msg.sender).transferEth(refundAmount);
242:            }
243:        } else {
```
The variable `maxTotalContributions` has already been cached on Line 228

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


## Modifier execution order can help save an entire SLOAD + an external call(Save 2100+ Gas)

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L841-L851
```solidity
File: /contracts/party/PartyGovernance.sol
841:    function emergencyExecute(
842:        address targetAddress,
843:        bytes calldata targetCallData,
844:        uint256 amountEth
845:    ) external payable onlyPartyDao onlyWhenEmergencyExecuteAllowed onlyDelegateCall {
846:        (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
847:        if (!success) {
848:            res.rawRevert();
849:        }
850:        emit EmergencyExecute(targetAddress, targetCallData, amountEth);
851:    }
```

The function above uses 3 modifies ie `onlyPartyDao` and `onlyWhenEmergencyExecuteAllowed` and `onlyDelegateCall`. 
According to solidity docs,modifiers are executed in the order they are presented meaning `onlyPartyDao` will be check first

Now if we peek into their implementation, we see the following 
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L231-L239
```solidity
    modifier onlyPartyDao() {
        {
            address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
            if (msg.sender != partyDao) {
                revert NotAuthorized();
            }
        }
        _;
    }
```
The modifier `onlyPartyDao()` makes an external call to `getAddress()` then checks it against `msg.sender` reverting when they don't match

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L251-L256
```solidity
    modifier onlyWhenEmergencyExecuteAllowed() {
        if (emergencyExecuteDisabled) {
            revert OnlyWhenEmergencyActionsAllowedError();
        }
        _;
    }
```
The modifier `onlyWhenEmergencyExecuteAllowed` involves reading a state variable `emergencyExecuteDisabled`

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/utils/Implementation.sol#L22-L27
```solidity
    modifier onlyDelegateCall() virtual {
        if (address(this) == implementation) {
            revert OnlyDelegateCallError();
        }
        _;
    }
```

The last modifier `onlyDelegateCall()` simply reads an Immutable variable which in terms of gas consumption is way cheaper compared to the other modifiers.
since we do revert if any of the modifier condition is not met, it would be wise to first check the condition in the modifier `onlyDelegateCall()` since it's way cheaper.
This way , if we do revert , the gas spent would be lower.
Refactor the code as follows.
```diff
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..19104d2 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -842,7 +842,7 @@ abstract contract PartyGovernance is
         address targetAddress,
         bytes calldata targetCallData,
         uint256 amountEth
-    ) external payable onlyPartyDao onlyWhenEmergencyExecuteAllowed onlyDelegateCall {
+    ) external payable onlyDelegateCall onlyPartyDao onlyWhenEmergencyExecuteAllowed  {
         (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
         if (!success) {
             res.rawRevert();
```

## Optimize this code by Only casting if indeed `msg.value > 0`(Saves gas for both >0 and == 0 scenarios)

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L138-L151
```solidity
File: /contracts/crowdfund/InitialETHCrowdfund.sol
138:        // If the deployer passed in some ETH during deployment, credit them
139:        // for the initial contribution.
140:        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
141:        if (initialContribution > 0) {
142:            // If this contract has ETH, either passed in during deployment or
143:            // pre-existing, credit it to the `initialContributor`.
144:            _contribute(
145:                crowdfundOpts.initialContributor,
146:                crowdfundOpts.initialDelegate,
147:                initialContribution,
148:                0,
149:                ""
150:            );
151:        }
```

In the above, we are casting the value of `msg.value` then checking if it is greater than 0, we should do the opposite, validate if it's greater than 0 and only cast if so
```diff
diff --git a/contracts/crowdfund/InitialETHCrowdfund.sol b/contracts/crowdfund/InitialETHCrowdfund.sol
index 29ade81..f657082 100644
--- a/contracts/crowdfund/InitialETHCrowdfund.sol
+++ b/contracts/crowdfund/InitialETHCrowdfund.sol
@@ -137,8 +137,8 @@ contract InitialETHCrowdfund is ETHCrowdfundBase {

         // If the deployer passed in some ETH during deployment, credit them
         // for the initial contribution.
-        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
-        if (initialContribution > 0) {
+        if (msg.value > 0) {
+            uint96 initialContribution = msg.value.safeCastUint256ToUint96();
             // If this contract has ETH, either passed in during deployment or
             // pre-existing, credit it to the `initialContributor`.
             _contribute(
```

## PartyGovernanceNFT.sol.setRageQuit(): We can optimize this function

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/party/PartyGovernanceNFT.sol#L318-L323
```solidity
File: /contracts/party/PartyGovernanceNFT.sol
318:    function setRageQuit(uint40 newRageQuitTimestamp) external {
319:        _assertHost();
320:        // Prevent disabling ragequit after initialization.
321:        if (newRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY) {
322:            revert CannotDisableRageQuitAfterInitializationError();
323:        }
```

The function `setRageQuit` calls an internal function `_assertHost()`  which has the following implementation
https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/party/PartyGovernance.sol#L216-L220
```solidity
    function _assertHost() internal view {
        if (!isHost[msg.sender]) {
            revert NotAuthorized();
        }
    }
```

This function, makes a state read `isHost[msg.sender]` and reverts if the check does not pass
If we go back to the main function ie `setRageQuit()` we see there is a second check ie ` if (newRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY) ` which only reads a function parameter and compares it against a constant variable. 
The second check is very cheap compared to the `isHost` check. If we call the function `_assertHost()`  first then we fail the second check, we would have wasted an entire SLOAD.
Reordering this checks allows us to minimize the gas consumed in case of a revert


```diff
diff --git a/contracts/party/PartyGovernanceNFT.sol b/contracts/party/PartyGovernanceNFT.sol
index 997b00c..765d403 100644
--- a/contracts/party/PartyGovernanceNFT.sol
+++ b/contracts/party/PartyGovernanceNFT.sol
@@ -316,11 +316,11 @@ contract PartyGovernanceNFT is PartyGovernance, ERC721, IERC2981 {
     /// @notice Set the timestamp until which ragequit is enabled.
     /// @param newRageQuitTimestamp The new ragequit timestamp.
     function setRageQuit(uint40 newRageQuitTimestamp) external {
-        _assertHost();
         // Prevent disabling ragequit after initialization.
         if (newRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY) {
             revert CannotDisableRageQuitAfterInitializationError();
         }
+        _assertHost();
```

## Avoid making any SSTOREs before validating all parameters(Save some SSTORES in case of reverts)

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L140-L172
```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
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

To avoid wasting gas making sstores, it is recommended we do all the checks first

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..7d78cb1 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -138,31 +138,31 @@ contract ETHCrowdfundBase is Implementation {
     // Initialize storage for proxy contracts, credit initial contribution (if
     // any), and setup gatekeeper.
     function _initialize(ETHCrowdfundOptions memory opts) internal {
-        // Set the minimum and maximum contribution amounts.
         if (opts.minContribution > opts.maxContribution) {
             revert MinGreaterThanMaxError(opts.minContribution, opts.maxContribution);
         }
-        minContribution = opts.minContribution;
-        maxContribution = opts.maxContribution;
-        // Set the min total contributions.
         if (opts.minTotalContributions > opts.maxTotalContributions) {
             revert MinGreaterThanMaxError(opts.minTotalContributions, opts.maxTotalContributions);
         }
-        minTotalContributions = opts.minTotalContributions;
-        // Set the max total contributions.
         if (opts.maxTotalContributions == 0) {
             // Prevent this because when `maxTotalContributions` is 0 the
             // crowdfund is invalid in `getCrowdfundLifecycle()` meaning it has
             // never been initialized.
             revert MaxTotalContributionsCannotBeZeroError(opts.maxTotalContributions);
         }
+        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
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

## Due to how short circuit works, we can save an entire SLOAD by performing sloads only when needed(Save 2100 Gas)

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L260-L267
```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
260:        // Subtract fee from contribution amount if applicable.
261:        address payable fundingSplitRecipient_ = fundingSplitRecipient;
262:        uint16 fundingSplitBps_ = fundingSplitBps;
263:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
264:            // Removes funding split from contribution amount in a way that
265:            // avoids rounding errors for very small contributions <1e4 wei.
266:            amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
267:        }
```
The condition check on line 263 tries to validate two variables ie `fundingSplitRecipient_` and `fundingSplitBps_`. For the code inside the block to be executed, both of this checks should pass ie `fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0`
The variables `fundingSplitRecipient_` and `fundingSplitBps_` are local variables that have been cached in the lines above and represent the state variables `fundingSplitRecipient_` and `fundingSplitBps_` respectively.
To assign the local variables, it means , we are making two state loads(SLOADs) 
According to rules of short circuit if this check `fundingSplitRecipient_ != address(0)` fails, then we wouldn't bother do the second part of those checks, meaning the state read `uint16 fundingSplitBps_ = fundingSplitBps;` here would not be utilized thus we just wasted an entire SLOAD(2100 Gas since it's cold). We can rewrite the if statement to check the conditions separately which allows us to only do the second SLOAD after we confirm the first check passes.


```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..2498c69 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -259,11 +259,13 @@ contract ETHCrowdfundBase is Implementation {

         // Subtract fee from contribution amount if applicable.
         address payable fundingSplitRecipient_ = fundingSplitRecipient;
-        uint16 fundingSplitBps_ = fundingSplitBps;
-        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
+        if (fundingSplitRecipient_ != address(0)){
+            uint16 fundingSplitBps_ = fundingSplitBps;
+             if (fundingSplitBps_ > 0) {
             // Removes funding split from contribution amount in a way that
             // avoids rounding errors for very small contributions <1e4 wei.
             amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
+             }
         }

```

## Avoid an SLOAD by taking advantage of short circuit(1 SLOAD: 2100 Gas)

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L278-L289
```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
278:    function convertVotingPowerToContribution(
279:        uint96 votingPower
280:    ) public view returns (uint96 amount) {
281:        amount = (votingPower * 1e4) / exchangeRateBps;

283:        // Add back funding split to contribution amount if applicable.
284:        address payable fundingSplitRecipient_ = fundingSplitRecipient;
285:        uint16 fundingSplitBps_ = fundingSplitBps;
286:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
287:            amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
288:        }
289:    }
```

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..087a96f 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -282,9 +282,11 @@ contract ETHCrowdfundBase is Implementation {

         // Add back funding split to contribution amount if applicable.
         address payable fundingSplitRecipient_ = fundingSplitRecipient;
-        uint16 fundingSplitBps_ = fundingSplitBps;
-        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
-            amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
+        if (fundingSplitRecipient_ != address(0)) {
+            uint16 fundingSplitBps_ = fundingSplitBps;
+            if (fundingSplitBps_ > 0) {
+                 amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
+            }
         }
     }
```

## Avoid making one SLOAD by utilizing the rules of short circuit(Save 2100 Gas)

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L317-L326
```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
317:    function _finalize(uint96 totalContributions_) internal {
318:        // Finalize the crowdfund.
319:        delete expiry;

321:        // Transfer funding split to recipient if applicable.
322:        address payable fundingSplitRecipient_ = fundingSplitRecipient;
323:        uint16 fundingSplitBps_ = fundingSplitBps;
324:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
325:            totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
326:        }
```


```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..4528750 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -320,9 +320,11 @@ contract ETHCrowdfundBase is Implementation {

         // Transfer funding split to recipient if applicable.
         address payable fundingSplitRecipient_ = fundingSplitRecipient;
-        uint16 fundingSplitBps_ = fundingSplitBps;
-        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
-            totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
+        if (fundingSplitRecipient_ != address(0)) {
+            uint16 fundingSplitBps_ = fundingSplitBps;
+            if (fundingSplitBps_ > 0) {
+                totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
+            }
         }
```

## Prioritize making cheaper checks  first to reduce gas consumption in revert cases

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L339-L344
```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
339:    function sendFundingSplit() external returns (uint96 splitAmount) {
340:        // Check that the crowdfund is finalized.
341:        CrowdfundLifecycle lc = getCrowdfundLifecycle();
342:        if (lc != CrowdfundLifecycle.Finalized) revert WrongLifecycleError(lc);

344:        if (fundingSplitPaid) revert FundingSplitAlreadyPaidError();
```

We are calling the function `getCrowdfundLifecycle()` which has the following implementation
https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L175-L194
```solidity
175:    function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {
176:        if (maxTotalContributions == 0) {
177:            return CrowdfundLifecycle.Invalid;
178:        }

180:        uint256 expiry_ = expiry;
181:        if (expiry_ == 0) {
182:            return CrowdfundLifecycle.Finalized;
183:        }

185:        if (block.timestamp >= expiry_) {
186:            if (totalContributions >= minTotalContributions) {
187:                return CrowdfundLifecycle.Won;
188:            } else {
189:                return CrowdfundLifecycle.Lost;
190:            }
191:        }

193:        return CrowdfundLifecycle.Active;
194:    }
```
We have some checks to determine the value of the variable `lifecycle`. Worst case is we read several state variables ie `maxTotalContributions,expiry,totalContributions, minTotalContributions` to get the return value.

Going back to the function `sendFundingSplit()` we have another check ` if (fundingSplitPaid)` which only reads one state variable, this is cheaper compared to the check `lc != CrowdfundLifecycle.Finalized`, we can refactor the code as follows to save some gas in case of a revert

```diff
     /// @notice Send the funding split to the recipient if applicable.
     function sendFundingSplit() external returns (uint96 splitAmount) {
+        if (fundingSplitPaid) revert FundingSplitAlreadyPaidError();
+
         // Check that the crowdfund is finalized.
         CrowdfundLifecycle lc = getCrowdfundLifecycle();
         if (lc != CrowdfundLifecycle.Finalized) revert WrongLifecycleError(lc);

-        if (fundingSplitPaid) revert FundingSplitAlreadyPaidError();
-
         address payable fundingSplitRecipient_ = fundingSplitRecipient;
         uint16 fundingSplitBps_ = fundingSplitBps;
         if (fundingSplitRecipient_ == address(0) || fundingSplitBps_ == 0) {
```

## Validate all parameters first before performing any other operations if there's no side effects

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L275-L304
```solidity
File: /contracts/party/PartyGovernance.sol
275:    ) internal virtual {
276:        // Check BPS are valid.
277:        if (govOpts.feeBps > 1e4) {
278:            revert InvalidBpsError(govOpts.feeBps);
279:        }
280:        if (govOpts.passThresholdBps > 1e4) {
281:            revert InvalidBpsError(govOpts.passThresholdBps);
282:        }
283:        // Initialize the proposal execution engine.
284:        _initProposalImpl(
285:            IProposalExecutionEngine(_GLOBALS.getAddress(LibGlobals.GLOBAL_PROPOSAL_ENGINE_IMPL)),
286:            abi.encode(proposalEngineOpts)
287:        );
288:        // Set the governance parameters.
289:        _getSharedProposalStorage().governanceValues = GovernanceValues({
290:            voteDuration: govOpts.voteDuration,
291:            executionDelay: govOpts.executionDelay,
292:            passThresholdBps: govOpts.passThresholdBps,
293:            totalVotingPower: govOpts.totalVotingPower
294:        });
295:        numHosts = uint8(govOpts.hosts.length);
296:        // Set fees.
297:        feeBps = govOpts.feeBps;
298:        feeRecipient = govOpts.feeRecipient;
299:        // Set the precious list.
300:        _setPreciousList(preciousTokens, preciousTokenIds);
301:        // Set the party hosts.
302:        if (govOpts.hosts.length > type(uint8).max) {
303:            revert TooManyHosts();
304:        }
```

We are making some state writes(SSTORE) then a the end we have an if check to validate that `govOpts.hosts.length > type(uint8).max` . If the check does not pass, the code reverts. All the SSTOREs will also be reverted. As such it would be more gas efficient to first do the validation and only write to state if all checks pass.

```diff
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..54fbe84 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -280,6 +280,9 @@ abstract contract PartyGovernance is
         if (govOpts.passThresholdBps > 1e4) {
             revert InvalidBpsError(govOpts.passThresholdBps);
         }
+        if (govOpts.hosts.length > type(uint8).max) {
+            revert TooManyHosts();
+        }
         // Initialize the proposal execution engine.
         _initProposalImpl(
             IProposalExecutionEngine(_GLOBALS.getAddress(LibGlobals.GLOBAL_PROPOSAL_ENGINE_IMPL)),
@@ -299,9 +302,6 @@ abstract contract PartyGovernance is
         // Set the precious list.
         _setPreciousList(preciousTokens, preciousTokenIds);
         // Set the party hosts.
-        if (govOpts.hosts.length > type(uint8).max) {
-            revert TooManyHosts();
-        }
         for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
             isHost[govOpts.hosts[i]] = true;
         }
```


## Uncheck arithmetic operations that cannot underflow/overflow(The bot missed these)

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1033-L1039
```solidity
File: /contracts/party/PartyGovernance.sol
1033:        if (n != 0) {
1034:            VotingPowerSnapshot memory lastSnap = voterSnaps[n - 1];
1035:            if (lastSnap.timestamp == snap.timestamp) {
1036:                voterSnaps[n - 1] = snap;
1037:                return;
1038:            }
1039:        }
```

Due to the check on line 1033, we know n will always be greater than 0 so the lowest value will be 1. 
This means our subtraction  operation `n-1` cannot overflow. We can wrap the whole of this in unchecked blocked 


https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1048-L1050
```solidity
File: /contracts/party/PartyGovernance.sol
1048:        if (n != 0) {
1049:            snap = voterSnaps[n - 1];
1050:        }
```
The operation `n-1` cannot overflow due to the check on Line 1048


https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L238
```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
238:            uint96 refundAmount = newTotalContributions - maxTotalContributions;
```
The operation `newTotalContributions - maxTotalContributions` cannot overflow since it can only be performed  if `newTotalContributions >= maxTotalContributions_` where `maxTotalContributions_` == `maxTotalContributions`

