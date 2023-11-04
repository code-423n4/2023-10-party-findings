# Code
## ETHCrowdfundBase.sol
### https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L396


# Vulnerability details
### ``` ETHCrowdfundBase.sol::disableEmergencyExecute()``` 
### ```emergencyExecuteDisabled = true; ``` is modifying the value of the emergencyExecuteDisabled state variable, which involves a storage write operation, and it will consume more gas.


# Tools Used :

###  Manual Review

# Recommended Mitigation Steps

###  Use ```!emergencyExecuteDisabled ``` instead of ``` emergencyExecuteDisabled = true;```
