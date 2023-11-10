## [G-01] Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct and subsequent reads as well, as writes have smaller gas savings.
````solidity
contracts/crowdfund/ETHCrowdfundBase.sol

33:    struct ETHCrowdfundOptions {
34:        Party party;
35:        address payable initialContributor;
36:        address initialDelegate;
37:        uint96 minContribution;
38:        uint96 maxContribution;
39:        bool disableContributingForExistingCard;
40:        uint96 minTotalContributions;
41:        uint96 maxTotalContributions;
42:        uint16 exchangeRateBps;
43:        uint16 fundingSplitBps;
44:        address payable fundingSplitRecipient;
45:        uint40 duration;
46:        IGateKeeper gateKeeper;
47:        bytes12 gateKeeperId;
48:    }
````
[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L33-L48](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L33-L48)
## Recommendations
Can save 1 storage slot by changing to:
````solidity
struct ETHCrowdfundOptions {
    Party party;                                       // slot0   (4 bytes)
    address payable initialContributor;               // slot0   (20 bytes)
    address initialDelegate;                          // slot1   (20 bytes)
    uint40 duration;                                 // slot2   (5 bytes)
    uint96 minContribution;                           // slot2   (12 bytes)
    uint96 maxContribution;                           // slot2   (12 bytes)
    uint16 exchangeRateBps;                          // slot3   (2 bytes)
    uint16 fundingSplitBps;                          // slot3   (2 bytes)
    bool disableContributingForExistingCard;         // slot3   (1 byte)
    uint96 minTotalContributions;                     // slot4   (12 bytes)
    uint96 maxTotalContributions;                     // slot4   (12 bytes)
    address payable fundingSplitRecipient;            // slot5   (20 bytes)
    IGateKeeper gateKeeper;                          // slot6   (20 bytes)
    bytes12 gateKeeperId;                             // slot7   (12 bytes)
}
````
## [G-02] With assembly, `.call (bool success)` transfer can be done gas-optimized
Return data (bool success,) has to be stored due to EVM architecture. But in a usage like below, ‘out’ and ‘outsize’ values are given (0,0); this storage disappears and gas optimization is provided.
````diff
- (bool success,) = dest.call{value:amount}("");

+ assembly {
+    success := call(gas(), dest, amount, 0, 0) 
+   }
````
[Reference](https://code4rena.com/reports/2023-01-biconomy#g-01-with-assembly-call-bool-success-transfer-can-be-done-gas-optimized)
````solidity
contracts/crowdfund/ETHCrowdfundBase.sol
379:        (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
````
[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L379](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L379)
````solidity
contracts/party/PartyGovernance.sol
846:        (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
````
[https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L846](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L846)