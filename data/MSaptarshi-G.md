# Code
##  ``` ETHCrowdfundBase.sol :: disableEmergencyExecute() ```
### https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L396

## ``` ETHCrowdfundBase.sol :: sendFundingSplit() ```
### https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L352 


# Vulnerability details
### ``` ETHCrowdfundBase.sol::disableEmergencyExecute()``` 
### ```emergencyExecuteDisabled = true; ``` is modifying the value of the emergencyExecuteDisabled state variable, which involves a storage write operation, and it will consume more gas.

### ``` ETHCrowdfundBase.sol::sendFundingSplit() ``` 
### ```fundingSplitPaid = true; ``` is modifying the value of the fundingSplitPaid state variable, which involves a storage write operation, and it will consume more gas.


# Tools Used :

###  Manual Review

# Recommended Mitigation Steps

###  Use ```!emergencyExecuteDisabled ``` instead of ``` emergencyExecuteDisabled = true;```

###  Use ```!fundingSplitPaid``` instead of ``` fundingSplitPaid= true;```