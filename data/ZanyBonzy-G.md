1. `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)`

- EthCrowdFundBase [L240](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L240), [L325](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L325)
`               amount -= refundAmount; ` 
`          totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4; `

- InitialEthCrowdFund [L212](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L212), [L268](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L268)
`            ethAvailable -= args.values[i];`
`            valuesSum += args.values[i];`

- PartyGovernance [L1008](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1008)
`                newDelegateDelegatedVotingPower -= oldSnap.intrinsicVotingPower; `

- PartyGovernanceNFT [L249](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L249), [L257](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L257), [L290](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L290), [L404](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L404), [L404](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L416)
`        _getSharedProposalStorage().governanceValues.totalVotingPower += votingPower; `
`        _getSharedProposalStorage().governanceValues.totalVotingPower -= votingPower;`
`            totalVotingPowerBurned += votingPower; `
`            _getSharedProposalStorage().governanceValues.totalVotingPower -= totalVotingPowerBurned;`
`                    amount -= fee;`