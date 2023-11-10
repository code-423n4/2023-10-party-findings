## Low Issues

| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | No check to see if the length of tokenIds & values match | 1 |
| [L-2](#L-2) | `batchRefund()` won't revert if the whole function fails | 1 |
| [L-3](#L-3) | No check to see the length of `customMetadata` provided when creating a party  | 1 |


## [L-1] No check to see if the length of tokenIds & values match

There is no check to see if the length of the token ids & the length of values entered match when calling `batchContribute()`.

```solidity
File: InitialETHCrowdfund.sol

    function batchContribute(
        BatchContributeArgs calldata args
    ) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {
        uint256 numContributions = args.tokenIds.length;
        votingPowers = new uint96[](numContributions);

        uint256 ethAvailable = msg.value;
        for (uint256 i; i < numContributions; ++i) {
            ethAvailable -= args.values[i];
     .....
```
## [L-2] `batchRefund()` won't revert if the whole function fails

If `revertOnFailure` is set to `false` then **worst case** is if the entire call to fails to all the token ids, the function would still won't revert. Unnecessary wastage of gas.

```solidity
File: InitialETHCrowdfund.sol

    function batchRefund(
        uint256[] calldata tokenIds,
        bool revertOnFailure
    ) external returns (uint96[] memory amounts) {
        uint256 numRefunds = tokenIds.length;
        amounts = new uint96[](numRefunds);

        for (uint256 i; i < numRefunds; ++i) {
            (bool s, bytes memory r) = address(this).call(
                abi.encodeCall(this.refund, (tokenIds[i]))
            );

            if (!s) {
                if (revertOnFailure) {    /// @note - if false, then the whole may fail & still not revert
                    r.rawRevert();
                }
            } else {
                amounts[i] = abi.decode(r, (uint96));
            }
        }
    }
```

## [L-3] No check to see the length of `customMetadata` provided when creating a party 

It is optional to provide metadeta when creating a new party but the size of the metadeta entered is not checked.
If the data entered is quite long (length of customMetadata is large), there are possible chances that creating a party would be too expensive & possibly run out of gas.

```solidity
File: InitialETHCrowdfund.sol

    function initialize(
        InitialETHCrowdfundOptions memory crowdfundOpts,
        ETHPartyOptions memory partyOpts,
        MetadataProvider customMetadataProvider,
        bytes memory customMetadata
    ) external payable onlyInitialize {
        // Create party the initial crowdfund will be for.
        Party party_ = _createParty(partyOpts, customMetadataProvider, customMetadata);
        ......
```

### Recommended
Add a check to ensure the size of `customMetadata` during initializing.