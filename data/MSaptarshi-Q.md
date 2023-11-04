# Code:
## ``` ETHCrowdfundBase :: _finalize() ```
### https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L324 
 
# Impact:
### totalContributions_ can be negative , If it becomes negative the function may fail.

# Tools Used:
### Manual Review

# Recommendation:
### Use a require or revert statement for checking it .                                                                    