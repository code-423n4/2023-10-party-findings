# Possible Optimization 1
##  ``` ETHCrowdfundBase.sol :: disableEmergencyExecute() ```
### https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L396

## Vulnerability details:
### ```emergencyExecuteDisabled = true; ``` is modifying the value of the emergencyExecuteDisabled state variable, which involves a storage write operation, and it will consume more gas.

## Recommended Mitigation Steps:
###  Use ```!emergencyExecuteDisabled ``` instead of ``` emergencyExecuteDisabled = true;```



# Possible Optimization 2
## ``` ETHCrowdfundBase.sol :: sendFundingSplit() ```
### https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L352 

## Vulnerability details:
### ```fundingSplitPaid = true; ``` is modifying the value of the fundingSplitPaid state variable, which involves a storage write operation, and it will consume more gas.

## Recommended Mitigation Steps:
###  Use ```!fundingSplitPaid``` instead of ``` fundingSplitPaid= true;```



# Possible Optimization 3
## ``` ETHCrowdfundBase.sol::disableEmergencyExecute()``` 
### https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L380

## Vulnerability details:
### ```emergencyExecuteDisabled = true; ``` is modifying the value of the emergencyExecuteDisabled state variable, which involves a storage write operation, and it will consume more gas.

## Recommended Mitigation Steps:
###  Use ```!emergencyExecuteDisabled``` instead of ``` emergencyExecuteDisabled = true;```



# Possible Optimization 4
## ETHCrowdfundBase::emergencyExecute()
### https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L3804

## Vulnerability details:
### ``` ETHCrowdfundBase::emergencyExecute() ``` This code snippet uses an additional res.rawRevert(); function call when success is false. It may consume slightly more gas due to the extra function call.
It allows you to provide a custom error message in res when the external call fails.

## Recommended Mitigation Steps:
### Use  ``` require(success, "ExternalCallFailed") ``` instead of ``` if (!success) { res.rawRevert(); } ```.   
                                                                                                              