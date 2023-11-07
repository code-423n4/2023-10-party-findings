## Misleading comment description about functions in `InitialETHCrowdfund`
## Description
The `batchContribute()` and `batchContributeFor()` function execute loop to performs these actions in batches, the name is self explanatory. However the comments above these function claim that these function may not revert if one call fails, which is not true. 

```
    /// @notice `contributeFor()` in batch form.
@>  ///         May not revert if any individual contribution fails. 
    /// @param args The arguments for the batched `contributeFor()` calls. 
    /// @return votingPowers The voting power received for each contribution.
```
Stating false information always hinders the credibility of the communicator, thus these misleading comments could potentially impact the credibility of the codebase, as they provide incorrect information about the behavior of these functions.



## Links

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L200-L203

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L251-L254

## Fix
Delete misleading comments :

```
    /// @notice `contributeFor()` in batch form.
    /// @param args The arguments for the batched `contributeFor()` calls. 
    /// @return votingPowers The voting power received for each contribution.
```

```
    /// @notice `contribute()` in batch form.
    /// @param args The arguments to pass to each `contribute()` call.
    /// @return votingPowers The voting power received for each contribution.
```

## Missing checks for `fundingSplitBps`

## Description

There are no checks that prevent `fundingSplitBps` value from being higher than 1e4 during the initialization of the contract in `ETHCrowdfundBase.sol`. If the `fundingSplitBps` value  > 1e4 the contract wouldn't be able to process contribution. 
## Links
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L167-L168

## Fix

```
if(fundingSplitBps > 1e4) {
revert invalidFundingSplitBps();
}
```

## If `fundingSplitBps` == 1e4 `convertVotingPowerToContribution()` fails

## Description

In the `convertVotingPowerToContribution()` of the `ETHCrowdfundBase()` contract the view function will fail if the  `fundingSplitBps` = 1e4. This is because it should be theoritically impossible to have voting power if the `fundingSplitBps` is set to 1e4. 

```
        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
            amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
        }
```

## Links

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L278-L289

## Fix

We could prevent the function from failing if we first check the `fundingSplitRecipient`. 

```

    function convertVotingPowerToContribution(
        uint96 votingPower
    ) public view returns (uint96 amount) {
         uint16 fundingSplitBps_ = fundingSplitBps;
     @> if(fundingSplitBps == 1e4) {
         return 0;
        }
        amount = (votingPower * 1e4) / exchangeRateBps;

        // Add back funding split to contribution amount if applicable.
        
        address payable fundingSplitRecipient_ = fundingSplitRecipient;
        
        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
            amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
        }
    }
```

