## [QA-1] No need to declare `i` as uint256
In the `PartGovernance.sol` smart contract, within the `_initialize()` function, an initial check verifies the size of the hosts array against `type(uint8).max`. However, in the subsequent line, the code iterates over the hosts array using a loop variable i declared as type uint256. 
```js
function _initialize(
        GovernanceOpts memory govOpts,
        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
        IERC721[] memory preciousTokens,
        uint256[] memory preciousTokenIds
    ) internal virtual {
       ...
        // Set the party hosts.
       @>  if (govOpts.hosts.length > type(uint8).max) {
            revert TooManyHosts();
        }
         // @audit : it is safe to use uint8 instead of uint256 b/c of above check
        @> for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
            isHost[govOpts.hosts[i]] = true;
        }
    }
```
### Suggestion 
```diff
-  for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
+  for (uint8 i = 0; i < govOpts.hosts.length; ++i) {

```
## [QA-2] Not using `_assertActiveMember()` function to check for activeUsers inside `PartGovernance.sol:distribute`
```js
function distribute(
        uint256 amount,
        ITokenDistributor.TokenType tokenType,
        address token,
        uint256 tokenId
    ) external returns (ITokenDistributor.DistributionInfo memory distInfo) {
        _assertNotGloballyDisabled();
            ...
            // @audit use __assertActiveMemberFunction();
            // Must be an active member.
        @>    VotingPowerSnapshot memory snap = _getLastVotingPowerSnapshotForVoter(msg.sender);
        @>  if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {
        @>        revert NotAuthorized();
        @>   }
            ...
    }

```
### Suggestion
Use `_assertActive()` instead of calculating active user inside `distribute()`
```diff
-  VotingPowerSnapshot memory snap = _getLastVotingPowerSnapshotForVoter(msg.sender);
-  if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {
-        revert NotAuthorized();
-   }
+ _assertActiveMember();
```
### [QA-3] No check on Array length of `withdrawTokens` and `minWithdrawAmounts`
The `rageQuit()` assumes that the caller must provide same length  array of `minWithdrawAmounts` and `withdrawTokens`, however the function does not check for same length of this array. if the length of array is not same than the function will revert on index out of bound error which is not user friendly and also be hard to handle on integrating system like FE..
```js
function rageQuit(
        uint256[] calldata tokenIds,
        IERC20[] calldata withdrawTokens,
        uint256[] calldata minWithdrawAmounts,
        address receiver
    ) external {
        ...
        {
            uint16 feeBps_ = feeBps;
            for (uint256 i; i < withdrawTokens.length; ++i) {
           @>     IERC20 token = withdrawTokens[i];
                uint256 amount = withdrawAmounts[i];

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
            @>        uint256 minAmount = minWithdrawAmounts[i];

                    // Check amount is at least minimum.
                    if (amount < minAmount) {
                        revert BelowMinWithdrawAmountError(amount, minAmount);
                    }

                    // Transfer token from party to recipient.
                    if (address(token) == ETH_ADDRESS) {
                        payable(receiver).transferEth(amount);
                    } else {
                        token.compatTransfer(receiver, amount);
                    }
                }
            }
        }
        ...

    }

```

## Suggestion
Add assertion on array length if not same length than throw a custom error.
```diff
+ if(withdrawTokens.length != minWithdrawAmounts.length) revert CustomError();
```

