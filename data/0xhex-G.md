# Gas Optimization

# Summary

| Number | Gas Optimization                                                                                        | Context |
| :----: | :------------------------------------------------------------------------------------------------------ | :-----: |
| [G-01] | Expresions for constant values such as a call to keccak256(), should sue immutable rather than constant |    1    |
| [G-02] | assert() should be replaced with require() or revert() to save gas                                      |    3    |
| [G-03] | Use constants instead of type(uintx).max                                                                |   13    |
| [G-04] | Use assembly to write address storage values                                                            |    2    |
| [G-05] | abi.encode() is less efficient than abi.encodePacked()                                                  |    1    |
| [G-06] | Do-While loops are cheaper than for loops                                                               |   10    |
| [G-07] | Always use Named Returns                                                                                |    3    |
| [G-08] | Refactor event to avoid emitting empty data                                                             |    2    |
| [G-09] | Using calldata instead of memory for read-only arguments in external functions saves gas                |    8    |
| [G-10] | Use assembly to reuse memory space when making more than one external call                              |   17    |
| [G-11] | Avoid Zero Transfer to save gas                                                                         |    3    |
| [G-12] | Use custom errors rather than require() strings to save gas                                             |    2    |
| [G-13] | Expensive operation inside a for loop waste of alot gas                                                 |    1    |

# Gas-Optimization Reprot For Party Protocol

## [G-01] Expresions for constant values such as a call to keccak256(), should sue immutable rather than constant

```solidity
File: proposals/ProposalExecutionEngine.sol

104    uint256 private constant _STORAGE_SLOT = uint256(keccak256("ProposalExecutionEngine.Storage"));
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L104

## [G-02] assert() should be replaced with require() or revert() to save gas

```solidity
File: party/PartyGovernance.sol

533        assert(tokenType == ITokenDistributor.TokenType.Erc20);
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol

```solidity
File: party/PartyGovernanceNFT.sol

499        assert(false); // Will not be reached.
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol

```solidity
File: proposals/ProposalExecutionEngine.sol

168                assert(currentInProgressProposalId == 0);
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol

## [G-03] Use constants instead of type(uintx).max

```solidity
File: proposals/ProposalExecutionEngine.sol

185            stor.nextProgressDataHash = bytes32(type(uint256).max);

314        require(uint8(proposalType) <= uint8(type(ProposalType).max));
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L185

```solidity
File: party/PartyGovernance.sol

302        if (govOpts.hosts.length > type(uint8).max) {

359        return getVotingPowerAt(voter, timestamp, type(uint256).max);

445        return high == 0 ? type(uint256).max : high - 1;

520        emit BatchMetadataUpdate(0, type(uint256).max);

581        emit BatchMetadataUpdate(0, type(uint256).max);

655            emit BatchMetadataUpdate(0, type(uint256).max);

688        emit BatchMetadataUpdate(0, type(uint256).max);

833        emit BatchMetadataUpdate(0, type(uint256).max);

897        emit BatchMetadataUpdate(0, type(uint256).max);

926            if (hintIndex != type(uint256).max) {

1083        if (pv.votes == type(uint96).max) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L302

## [G-04] Use assembly to write address storage values

```solidity
File: party/PartyGovernance.sol

198    address payable public feeRecipient;

```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L198C28-L198C40

```solidity
File: utils/Implementation.sol

12    address public immutable implementation;
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol#L12C1-L13C1

## [G-05] abi.encode() is less efficient than abi.encodePacked()

```solidity
File: party/PartyGovernance.sol

286            abi.encode(proposalEngineOpts)
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol

## [G-06] Do-While loops are cheaper than for loops

```solidity
File: crowdfund/InitialETHCrowdfund.sol

211        for (uint256 i; i < numContributions; ++i) {

260        for (uint256 i; i < args.recipients.length; ++i) {

357        for (uint256 i; i < numRefunds; ++i) {

379        for (uint i = 0; i < authoritiesLength - 1; ++i) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L211

```solidity
File: party/PartyGovernance.sol

305        for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L305

```solidity
File: party/PartyGovernanceNFT.sol

102            for (uint256 i; i < authorities.length; ++i) {

272        for (uint256 i; i < tokenIds.length; ++i) {

378            for (uint256 i; i < withdrawTokens.length; ++i) {

391                for (uint256 j; j < tokenIds.length; ++j) {

408            for (uint256 i; i < withdrawTokens.length; ++i) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L102

## [G-07] Always use Named Returns

```solidity
File: utils/Implementation.sol
40    function IMPL() external view returns (address) {
41        return implementation;
42    }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol#L40-L42

```solidity
File: party/PartyGovernance.sol

932        return snap;

1061        return flags;
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L932

## [G-08] Refactor event to avoid emitting empty data

```solidity
File: crowdfund/ETHCrowdfundBase.sol

335        emit Finalized();

397        emit EmergencyExecuteDisabled();
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L335

By refactoring events to opt-out of emitting empty encoded data, unnecessary gas consumption can be reduced in contracts that emit many events.

## [G-09] Using calldata instead of memory for read-only arguments in external functions saves gas

```solidity
File: party/PartyGovernanceNFT.sol

263    function burn(uint256[] memory tokenIds) public {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#263

```solidity
File: contracts/party/PartyGovernance.sol

706    function execute(
707        uint256 proposalId,
708        Proposal memory proposal,
709        IERC721[] memory preciousTokens,
710        uint256[] memory preciousTokenIds,
711        bytes calldata progressData,
712        bytes calldata extraData
713    ) external payable {

1137    function _setPreciousList(
1138        IERC721[] memory preciousTokens,
1139        uint256[] memory preciousTokenIds

1154    function _hashPreciousList(
1155        IERC721[] memory preciousTokens,
1156        uint256[] memory preciousTokenIds
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L706

## [G-10] Use assembly to reuse memory space when making more than one external call

```solidity
File: crowdfund/InitialETHCrowdfund.sol

302            party.mint(contributor, votingPower, delegate);
305        } else if (party.ownerOf(tokenId) == contributor) {
307            party.increaseVotingPower(tokenId, votingPower);



328        uint96 votingPower = party.votingPowerByTokenId(tokenId).safeCastUint256ToUint96();
333            address payable contributor = payable(party.ownerOf(tokenId));
336            party.burn(tokenId);

```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L302

```solidity
File: signature-validators/OffChainSignatureValidator.sol

59        uint96 signerVotingPowerBps = party.getVotingPowerAt(signer, uint40(block.timestamp)) *
62        if (signerVotingPowerBps == 0 && party.balanceOf(signer) == 0) {
67        uint96 totalVotingPower = party.getGovernanceValues().totalVotingPower;
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L59

```solidity
File: party/PartyGovernance.sol

515            _GLOBALS.getAddress(LibGlobals.GLOBAL_TOKEN_DISTRIBUTOR)
526                distributor.createNativeDistribution{ value: amount }(
534        IERC20(token).compatTransfer(address(distributor), amount);
536            distributor.createErc20Distribution(
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L515

```soldity
File: proposals/ProposalExecutionEngine.sol

232            return validator.isValidSignature(hash, signature);
239                    _GLOBALS.getAddress(LibGlobals.GLOBAL_OFF_CHAIN_SIGNATURE_VALIDATOR)
242            return validator.isValidSignature(hash, signature);
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L232

## [G-11] Avoid Zero Transfer to save gas

```solidity
File: crowdfund/ETHCrowdfundBase.sol

333        payable(address(party)).transferEth(totalContributions_);

356        payable(fundingSplitRecipient_).transferEth(splitAmount);
```

https://code4rena.com/contests/contracts/crowdfund/ETHCrowdfundBase.sol#L333

```solidity
File: party/PartyGovernanceNFT.sol

453        _transferVotingPower(owner, to, votingPowerByTokenId[tokenId]);
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol

## [G-12] Use custom errors rather than require() strings to save gas

```solidity
File: proposals/ProposalExecutionEngine.sol

313        require(proposalType != ProposalType.Invalid);
314        require(uint8(proposalType) <= uint8(type(ProposalType).max));
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L313

## [G-13] Expensive operation inside a for loop waste of alot gas

```solidity
File:

408        for (uint256 i; i < withdrawTokens.length; ++i) {
408                IERC20 token = withdrawTokens[i];
410                uint256 amount = withdrawAmounts[i];
411
412                // Take fee from amount.
413                uint256 fee = (amount * feeBps_) / 1e4;
414
415                if (fee > 0) {
416                    amount -= fee;
417
418                    // Transfer fee to fee recipient.
419                    if (address(token) == ETH_ADDRESS) {
420                        payable(feeRecipient).transferEth(fee);
421                    } else {
422                        token.compatTransfer(feeRecipient, fee);
423                    }
424                }
425
426                if (amount > 0) {
427                    uint256 minAmount = minWithdrawAmounts[i];
428
429                    // Check amount is at least minimum.
430                    if (amount < minAmount) {
431                        revert BelowMinWithdrawAmountError(amount, minAmount);
432                    }
433
434                    // Transfer token from party to recipient.
435                    if (address(token) == ETH_ADDRESS) {
436                        payable(receiver).transferEth(amount);
437                    } else {
438                        token.compatTransfer(receiver, amount);
439                    }
440                }
441            }
```

By batching the token transfers, this optimization reduces repeating expensive per-transfer costs. This helps improve gas efficiency for rage quits involving multiple tokens and users.