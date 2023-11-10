## ETHCrowdfundBase
1) there's no re-enable emergencyExecute function inside the contract that re-enable that set the "emergencyExecuteDisable" variable to false, i suggest to change the "disableEmergencyExecute" function using a bool value as param and set the "emergencyExecuteDisabled" variable using the param value
https://github.com/PartyDAO/party-protocol/blob/37dae4292dde2547a3b1ced8a041f926e1b37d58/contracts/crowdfund/ETHCrowdfundBase.sol#L388-L399
2) line 166 should be checked that fundingSplitBps will be even lower to 1e4,because if not,that will cause underflow problems in several lines and functions calculations,like (266,287)
https://github.com/PartyDAO/party-protocol/blob/37dae4292dde2547a3b1ced8a041f926e1b37d58/contracts/crowdfund/ETHCrowdfundBase.sol#L166
https://github.com/PartyDAO/party-protocol/blob/37dae4292dde2547a3b1ced8a041f926e1b37d58/contracts/crowdfund/ETHCrowdfundBase.sol#L266
https://github.com/PartyDAO/party-protocol/blob/37dae4292dde2547a3b1ced8a041f926e1b37d58/contracts/crowdfund/ETHCrowdfundBase.sol#L287