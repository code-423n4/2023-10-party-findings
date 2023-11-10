## 1. Delete the PorposalState for the rejected proposals 
Proposals can be rejected/cancelled/Vetoed. Consider Implementing a function that removes the ProposalState for such proposals will not only free up storage but also save gas, which can be allocated to other phases. This improvement will contribute to the protocol's efficiency and its overall alignment with Ethereum best practices.

## 2. Gas Bomb for the final contributor
The `_processContribution()` function triggers the [`finalize()`](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L235) function when the last contributor's contribution fulfills the contribution goal. While this design is sound, it can lead to high gas costs for the final user, particularly if they're making a small contribution like 10 wei. It's worth considering whether it's necessary to call the `finalize()` function within `_processContribution()`, as doing so makes it accessible to anyone via `finalize()`.

## 3. Update solidity compiler version to 0.8.22 for better gas optimization
`0.8.22` solidity version offers better gas optimization as it adds the Unchecked loop increments.

Note: Beware it's newly released & Base don't support it yet so make sure security isn't sacrificed. Thanks!