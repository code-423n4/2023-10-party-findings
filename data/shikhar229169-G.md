### Initializing variable costs more gas

If a variable is used which initial value is to be 0, then it should not be initialized to 0, because it is automatically done and doing it manually costs more gas.

*Instances (4)*:
```solidity
File: crowdfund/InitialETHCrowdfund.sol

379:         for (uint i = 0; i < authoritiesLength - 1; ++i) {

```

```solidity
File: party/PartyGovernance.sol

305:         for (uint256 i = 0; i < govOpts.hosts.length; ++i) {

432:         uint256 low = 0;

1054:        uint256 flags = 0;

```

### Recommendations
Do not initialize the variable to 0 if it is expected to have 0 as initial value. 