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