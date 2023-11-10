# [G-01] Redundant variable in `OffChainSignatureValidator.sol`

In function `isValidSignature()` variable `encodedPacket` is used only once, which means it does not need to be declared at all. Code can be changed to:

```
if (keccak256(abi.encodePacked(
            "\x19Ethereum Signed Message:\n", Strings.toString(message.length), message)) != hash) {
            revert MessageHashMismatch();
        }
```

# [G-02] Use constants instead of `type(uintX).max`

When you use type(uintX).max - it may result in higher gas costs because it involves a runtime operation to calculate the `type(uintX).max` at runtime. This calculation is performed every time the expression is evaluated.

To save gas, it is recommended to use constants to represent the maximum value. Declaration of a constant with the maximum value means that the value is known at compile-time and does not require any runtime calculations.


`ProposalExecutionEngine.sol`
```
185:	stor.nextProgressDataHash = bytes32(type(uint256).max);
```

`PartyGovernance.sol`
```
187:    uint96 private constant VETO_VALUE = type(uint96).max;
302:        if (govOpts.hosts.length > type(uint8).max) {
359:        return getVotingPowerAt(voter, timestamp, type(uint256).max);
445:        return high == 0 ? type(uint256).max : high - 1;
520:        emit BatchMetadataUpdate(0, type(uint256).max);
581:        emit BatchMetadataUpdate(0, type(uint256).max);
655:            emit BatchMetadataUpdate(0, type(uint256).max);
688:        emit BatchMetadataUpdate(0, type(uint256).max);
833:        emit BatchMetadataUpdate(0, type(uint256).max);
897:        emit BatchMetadataUpdate(0, type(uint256).max);
926:            if (hintIndex != type(uint256).max) {
1083:        if (pv.votes == type(uint96).max) {
```


# [G-03] Redesign `decreaseVotingPower()` in `PartyGovernanceNFT.sol`

`mintedVotingPower` is increased every time `increaseVotingPower()` is called for any `tokenId`.
`votingPowerByTokenId[tokenId]` is increased every time `increaseVotingPower(tokenId, ...)` is increased.

This allows us to assume, that `mintedVotingPower >= votingPowerByTokenId[tokenId]`

We can redesign `decreaseVotingPower()`:

```
    function decreaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        _assertAuthority();
        votingPowerByTokenId[tokenId] -= votingPower;
        unchecked { mintedVotingPower -= votingPower; }
        

        _adjustVotingPower(ownerOf(tokenId), -votingPower.safeCastUint96ToInt192(), address(0));
    }
```

If `votingPower > votingPowerByTokenId[tokenId]`, function will revert (due to underflow).
If function won't revert, then we know that `votingPower <= votingPowerByTokenId[tokenId]`.
But we also know, that since `mintedVotingPower >= votingPowerByTokenId[tokenId]`, thus `mintedVotingPower -= votingPower` won't revert too, thus this operation can be unchecked.



# [G-04] Length of arrays are calculated multiple of times in `PartyGovernanceNFT.sol`

* `withdrawTokens.length` is calculated three times in `PartyGovernanceNFT.sol`

[File: PartyGovernanceNFT.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L375-L378)
```
375:	 uint256[] memory withdrawAmounts = new uint256[](withdrawTokens.length);
376:        {
377:            IERC20 prevToken;
378:            for (uint256 i; i < withdrawTokens.length; ++i) {
(...)
408:	 for (uint256 i; i < withdrawTokens.length; ++i) {
```

Calculating length of array is costly operation. It's better to calculate it once, cache the result in variable and then use later. During the bot-report, it was only mentioned, that `withdrawTokens.length` is calculated on every loop iteration and should be cached. However, it should be cached even sooner. It is used in line 375, 378, 408.


* `tokenIds.length` is calculated two times in `PartyGovernanceNFT.sol`

[File: PartyGovernanceNFT.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L375-L378)
```
350:	if (tokenIds.length == 0) revert NothingToBurnError();
(...)
391:	for (uint256 j; j < tokenIds.length; ++j) {
```

Calculating length of array is costly operation. It's better to calculate it once, cache the result in variable and then use later. During the bot-report, it was only mentioned, that `tokenIds.length` is calculated on every loop iteration and should be cached. However, it should be cached even sooner. It is used in line 350, 391.



# [G-05] Use cached variable in `PartyGovernanceNFT.sol`

[File: PartyGovernanceNFT.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L189-L190)
```
        mintedVotingPower += votingPower_;
        votingPowerByTokenId[tokenId] = votingPower_;
```

`mintedVotingPower` is already assigned to local variable `uint96 mintedVotingPower_`. Above code should be changed to:

```
        mintedVotingPower = mintedVotingPower_ + votingPower_;
        votingPowerByTokenId[tokenId] = votingPower_;
```

[File: PartyGovernanceNFT.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L223-L224)
```
        mintedVotingPower += votingPower;
        uint256 newIntrinsicVotingPower = votingPowerByTokenId[tokenId] + votingPower;
```

`mintedVotingPower` is already assigned to local variable `uint96 mintedVotingPower_`. Above code should be changed to:

```
        mintedVotingPower = mintedVotingPower_ + votingPower;
        uint256 newIntrinsicVotingPower = votingPowerByTokenId[tokenId] + votingPower;
```


# [G-06] Move condition which might revert quicker on top in `PartyGovernance.sol`

In function `accept()`, below condition can be moved on top:

```
 if (lastRageQuitTimestamp == block.timestamp) {
            revert CannotRageQuitAndAcceptError();
        }
```
If function revert with `CannotRageQuitAndAcceptError()` ealier, it will save gas for other more costly operations (such as getting the status of proposal).

# [G-07] Cache `party` into memory variable in `InitialETHCrowdfund.sol`

`party` is being used multiple of times. It can be cached into memory variable:

[File: InitialETHCrowdfund.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L328-L336)
```
        uint96 votingPower = party.votingPowerByTokenId(tokenId).safeCastUint256ToUint96();
        amount = convertVotingPowerToContribution(votingPower);

        if (amount > 0) {
            // Get contributor to refund.
            address payable contributor = payable(party.ownerOf(tokenId));

            // Burn contributor's party card.
            party.burn(tokenId);
```

Cache `party` like this: `Party party_ = party;` and use `party_` instead of `party`.


# [G-08] `_createParty()` in `InitialETHCrowdfund.sol` can be optimized

[File: InitialETHCrowdfund.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L377-L382)
```
        uint256 authoritiesLength = opts.authorities.length + 1;
        address[] memory authorities = new address[](authoritiesLength);
        for (uint i = 0; i < authoritiesLength - 1; ++i) {
            authorities[i] = opts.authorities[i];
        }
        authorities[authoritiesLength - 1] = address(this);
```

Above code block can be optimized:
* we don't need to calculate `authoritiesLength - 1` on every loop iteration 
* we don't need to add 1 to `authoritiesLength`, just to subtract it later, we can add 1 at line 378 instead 


```
        uint256 authoritiesLength = opts.authorities.length;
        address[] memory authorities = new address[](authoritiesLength + 1); 
        for (uint i = 0; i < authoritiesLength; ++i) {
            authorities[i] = opts.authorities[i];
        }
        authorities[authoritiesLength] = address(this);
```

# [G-09] Use cached variable in `ETHCrowdfundBase.sol`

[File: ETHCrowdfundBase.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L238)
```
 uint96 refundAmount = newTotalContributions - maxTotalContributions;
```

`maxTotalContributions` is already cached into variable `maxTotalContributions_` at line 228: `uint96 maxTotalContributions_ = maxTotalContributions;`. You should use cached variable instead:

```
uint96 refundAmount = newTotalContributions - maxTotalContributions_;
```

# [G-10] Condition which might revert should be moved higher in `PartyGovernance.sol`

In function `_initialize()`, below check should be moved higher:

```
 if (govOpts.hosts.length > type(uint8).max) {
            revert TooManyHosts();
        }
```

If there are too many hosts provided, function will revert earlier, without wasting gas on initializing the proposal execution engine and setting the governance parameters.

# [G-11] Operations which won't underflow/overflow in `PartyGovernance.sol` can be unchecked.

* Decreasing number of hosts in `PartyGovernance.sol` can be unchecked

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L468)
```
--numHosts;
```

Function `abdicateHost()` can be called by existing host only, thus we know that `numHost > 0` and decreasing it can be unchecked.

* Increasing `lastProposalId` can be unchecked.
If this ever overflow, the whole protocol will be broken (it won't be possible to create new proposals)

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L558)
```
proposalId = ++lastProposalId;
```


# [G-12] There's no need to cast in `PartyGovernance.sol`

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L812)
```
 revert ProposalCannotBeCancelledYetError(
                    uint40(block.timestamp),
                    uint40(cancelTime)
```

There's no reason for casting `uint256` to `uint40`, just to include them in revert message. It's better to redesign `ProposalCannotBeCancelledYetError()` so that it accepts `uint256`.


# [G-13] Redundant variables in `PartyGovernance.sol`

Variable `partyDao` is used only once, thus it doesn't need to be declared at all:

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L233)
```
 address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
            if (msg.sender != partyDao) {
```

can be changed to:

```
if (msg.sender != _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET)) {
```

The same issue occurs at line 243-244.

# [G-14] Length of arrays are calculated multiple of times in `PartyGovernance.sol`

* `govOpts.hosts.length` is calculated three times in `PartyGovernance.sol`

[File: PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L295)
```
295:	numHosts = uint8(govOpts.hosts.length);
302:	if (govOpts.hosts.length > type(uint8).max) {
305:	for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
```

Calculating length of array is costly operation. It's better to calculate it once, cache the result in variable and then use later. During the bot-report, it was only mentioned, that `govOpts.hosts.length` is calculated on every loop iteration and should be cached. However, it should be cached even sooner. It is used in line 295, 302, 305.