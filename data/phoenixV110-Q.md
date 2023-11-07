# [L-01] Misleading comments state call may not revert but it reverts if any individual call fails

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L201
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L252C17-L252C68

## Description
Downstream call failure is not handled in `batchContributeFor()` and `batchContribute()` methods. Call reverts if any individual call fails in the loop.

## Recommendation
Use the same pattern as used in `batchRefund()` method by using the `.call{}()` in the loop or update the comments.

# [L-02] Check minContribution > maxTotalContributions missing in EthCrowdfundBase contract

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L140-L172

## Description
It is possible to provide `minContribution > maxTotalContributions` in the `_initialize()`. Which will cause `_processContribution()` to revert in each case. There is no method to update these values once initialised either. This will lead to developer having the deploy the contract again with correct values.

## Recommendation
```
if (minContribution > maxTotalContributions) {
	revert();
}
```

# [L-03] emergencyExecute() is enabled by default. It cannot be re-enabled once disabled.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L388-L398

## Description
`emergencyExecuteDisabled` variable is false by default. It is not set in `_initialize()`. It can be disabled only once in `disableEmergencyExecute()` method.

## Recommendation
+ `emergencyExecuteDisabled` should be initialised in the `_initialize()` method instead of setting it as enabled.
+ `disableEmergencyExecute()` should enable/disable `emergencyExecuteDisabled` variable.