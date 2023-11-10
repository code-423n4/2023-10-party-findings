
## Gas Optimizations

| Number | Issue | Instances | Total gas saved |
|--------|-------|-----------|-----------------|
|[G-01]| Possible Optimization in accept() function    |  1  | |
|[G-02]| Possible Optimization in _adjustVotingPower() function     |  1  | |
|[G-03]| Counting down in for statements is more gas efficient    |  9  | |
|[G-04]| Use a do while loop instead of a for loop    |  4  | |
|[G-05]| Use the existing memory value to do the addition and  subtraction instead of using state varia    |  2  | |
|[G-06]| Do not cache immutable values    |  1  | |
|[G-07]| Possible Optimizations in PartyGovernance.sol    |  8  | |
|[G-08]| Possible Optimization in PartyGovernance.sol    |  3  | |
|[G-09]| Possible Optimizations in PartyGovernance.sol    |  4  | |
|[G-10]| Pack structs by putting data types that can fit together next to each other    |  1  | |
|[G-11]| The function should first verify if msg.value > 0 before performing any other operation (Saves 7146 Gas)    |  1  | 7146 |
|[G-12]| Don’t cache calls that are only used once    |  2  | |
|[G-13]| public functions not called by the contract should be declared external instead    |  8  | |
|[G-14]| State variables which are not modified within functions should be set as constant or immutable for values set at deployment    |  1  | 10000 |
|[G-15]| Use uint256(1)/uint256(2) instead for true and false boolean states    |  13  | 13000 |
|[G-16]| Avoid Unnecessary Public Variables    |  11  | 242000 |
|[G-17]| Use constants instead of type(uintx).max    |  13  | |
|[G-18]| addition and subtraction  which won’t underflow can be unchecked   |  16  | 96 |
|[G-19]| Use assembly in place of abi.decode to extract calldata values more efficiently   |  6  | |
|[G-20]| Cache external calls outside of loop to avoid re-calling function on each iteration   |  1  | |
|[G-21]| Use assembly to perform efficient back-to-back calls   |  1  | |
|[G-22]| Use hardcoded address instead of address(this)    |  10  | |
|[G-23]| Use assembly for loops    |  4  | |
|[G-24]| mounts should be checked for 0 before calling a transfer    |  2  | |
|[G-25]| Empty blocks should be removed or emit something    |  1  | |
|[G-26]| Use Assembly To Check For address(0)    |  18  | |

## [G-01] Possible Optimization in accept() function 

In the accept function  the ProposalStateValues struct, the variable values is stored in memory. To save gas, it is recommended to use the cached value instead of repeatedly accessing info.values. By utilizing the cached value, unnecessary memory operations can be avoided.

```solidity
file:  contracts/party/PartyGovernance.sol

595       function accept(uint256 proposalId, uint256 snapIndex) public returns (uint256 totalVotes) {
        // Get the information about the proposal.
        ProposalState storage info = _proposalStateByProposalId[proposalId];
        ProposalStateValues memory values = info.values;

        // Can only vote in certain proposal statuses.
        {
            ProposalStatus status = _getProposalStatus(values);
            // Allow voting even if the proposal is passed/ready so it can
            // potentially reach 100% consensus, which unlocks special
            // behaviors for certain proposal types.
            if (
                status != ProposalStatus.Voting &&
                status != ProposalStatus.Passed &&
                status != ProposalStatus.Ready
            ) {
                revert BadProposalStatusError(status);
            }
        }

        // Prevent voting in the same block as the last rage quit timestamp.
        // This is to prevent an exploit where a member can rage quit to reduce
        // the total voting power of the party, then propose and vote in the
        // same block since `getVotingPowerAt()` uses `values.proposedTime - 1`.
        // This would allow them to use the voting power snapshot just before
        // their card was burned to vote, potentially passing a proposal that
        // would have otherwise not passed.
        if (lastRageQuitTimestamp == block.timestamp) {
            revert CannotRageQuitAndAcceptError();
        }

        // Cannot vote twice.
        if (info.hasVoted[msg.sender]) {
            revert AlreadyVotedError(msg.sender);
        }
        // Mark the caller as having voted.
        info.hasVoted[msg.sender] = true;

        // Increase the total votes that have been cast on this proposal.
        uint96 votingPower = getVotingPowerAt(msg.sender, values.proposedTime - 1, snapIndex);
        values.votes += votingPower;
        if (isHost[msg.sender]) {
            ++values.numHostsAccepted;
        }
        info.values = values;
        emit ProposalAccepted(proposalId, msg.sender, votingPower);

        // Update the proposal status if it has reached the pass threshold.
        if (
            values.passedTime == 0 &&
            _areVotesPassing(
                values.votes,
                values.totalVotingPower,
                _getSharedProposalStorage().governanceValues.passThresholdBps
            )
        ) {
            info.values.passedTime = uint40(block.timestamp);
            emit ProposalPassed(proposalId);
            // Notify third-party platforms that the governance NFT metadata has
            // updated for all tokens.
            emit BatchMetadataUpdate(0, type(uint256).max);
        }
        return values.votes;
    }


```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L595-L658


### Gas value:

Berfore gas value:
[PASS] testGovernance_allHostsAccept() (gas: 1166188)
[PASS] testGovernance_allHostsAccept_abdicateHost() (gas: 994388)
[PASS] testGovernance_allHostsAccept_andUnanimous() (gas: 950694)
[PASS] testGovernance_allHostsAccept_noHostVotes() (gas: 945630)
[PASS] testGovernance_allHostsAccept_noHostsVetoPeriod() (gas: 820886)

After gas value:                   
[PASS] testGovernance_allHostsAccept() (gas: 1101873)
[PASS] testGovernance_allHostsAccept_abdicateHost() (gas: 944955)
[PASS] testGovernance_allHostsAccept_andUnanimous() (gas: 873216)
[PASS] testGovernance_allHostsAccept_noHostVotes() (gas: 883125)
[PASS] testGovernance_allHostsAccept_noHostsVetoPeriod() (gas: 794830)


## [G-02] Possible Optimization in _adjustVotingPower() function 

In the accept function  the delegationsByVoter mapping, values is stored in memory variable. To save gas, it is recommended to use the cached value instead of repeatedly accessing delegationsByVoter[voter]. By utilizing the cached value, unnecessary memory operations can be avoided.

```solidity
file: contracts/party/PartyGovernance.sol

944   function _adjustVotingPower(address voter, int192 votingPower, address delegate) internal {
        VotingPowerSnapshot memory oldSnap = _getLastVotingPowerSnapshotForVoter(voter);
        address oldDelegate = delegationsByVoter[voter];
        // If `oldDelegate` is zero and `voter` never delegated, then have
        // `voter` delegate to themself.
        oldDelegate = oldDelegate == address(0) ? voter : oldDelegate;
        // If the new `delegate` is zero, use the current (old) delegate.
        delegate = delegate == address(0) ? oldDelegate : delegate;

        VotingPowerSnapshot memory newSnap = VotingPowerSnapshot({
            timestamp: uint40(block.timestamp),
            delegatedVotingPower: oldSnap.delegatedVotingPower,
            intrinsicVotingPower: (oldSnap.intrinsicVotingPower.safeCastUint96ToInt192() +
                votingPower).safeCastInt192ToUint96(),
            isDelegated: delegate != voter
        });
        _insertVotingPowerSnapshot(voter, newSnap);
        delegationsByVoter[voter] = delegate;

        // This event is emitted even if the delegate did not change.
        emit PartyDelegateUpdated(voter, delegate);

        // Handle rebalancing delegates.
        _rebalanceDelegates(voter, oldDelegate, delegate, oldSnap, newSnap);
    }


```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L944-L968

### Gas value:

Berfore gas value:
[PASS] testMintPartCard_aboveTotalVotingPower() (gas: 647610)

After gas value:
[PASS] testMintPartCard_aboveTotalVotingPower() (gas: 607260)


## [G‑03] Counting down in for statements is more gas efficient

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable.

```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

211   for (uint256 i; i < numContributions; ++i) {

260   for (uint256 i; i < args.recipients.length; ++i) {

357   for (uint256 i; i < numRefunds; ++i) {

379   for (uint i = 0; i < authoritiesLength - 1; ++i) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L211

Foundry test only for batchContribut function 

Before gas value:
[PASS] test_batchContribute_works() (gas: 1058569)

After Gas value:
[PASS] test_batchContribute_works() (gas: 759534)


```solidity
file: contracts/party/PartyGovernance.sol

305    for (uint256 i = 0; i < govOpts.hosts.length; ++i) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L305

```solidity
file: contracts/party/PartyGovernanceNFT.sol

102   for (uint256 i; i < authorities.length; ++i) {

272   for (uint256 i; i < tokenIds.length; ++i) {

378   for (uint256 i; i < withdrawTokens.length; ++i) {

391   for (uint256 j; j < tokenIds.length; ++j) {

408   for (uint256 i; i < withdrawTokens.length; ++i) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L102

### foundry test only for rageQuit() 

Before gas value:
[PASS] testRageQuit_cannotQuitAndAcceptInSameBlock() (gas: 2942393)
[PASS] testRageQuit_enableRageQuit() (gas: 2668809)
[PASS] testRageQuit_multiple() (gas: 3116513)
[PASS] testRageQuit_revertIfBelowMinWithdrawAmount() (gas: 2650128)
[PASS] testRageQuit_revertsIfNotOwner() (gas: 2586414)

After gas value:
[PASS] testRageQuit_cannotQuitAndAcceptInSameBlock() (gas: 2821109)
[PASS] testRageQuit_enableRageQuit() (gas: 2617949)
[PASS] testRageQuit_multiple() (gas: 3006943)
[PASS] testRageQuit_revertIfBelowMinWithdrawAmount() (gas: 2593395)
[PASS] testRageQuit_revertsIfNotOwner() (gas: 2575401)


### Test Code

```solidity
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    function testGas() public {
        c0.AddNum();
        c1.AddNum();
    }
}
contract Contract0 {
    uint256 num = 3;
    function AddNum() public {
        uint256 _num = num;
        for(uint i=0;i<=9;i++){
            _num = _num +1;
        }
        num = _num;
    }
}
contract Contract1 {
    uint256 num = 3;
    function AddNum() public {
        uint256 _num = num;
        for(uint i=9;i>=0;i--){
            _num = _num +1;
        }
        num = _num;
    }
}
```

## [G-04] Use a do while loop instead of a for loop

A do while loop will cost less gas since the condition is not being checked for the first iteration.


```solidity
file: contracts/party/PartyGovernanceNFT.sol

391    for (uint256 j; j < tokenIds.length; ++j) {
                    // Must be retrieved before burning the token.
                    withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
                }           
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L391

### foundry test is used only for rageQuit() function 

Before gas value:
[PASS] testRageQuit_cannotQuitAndAcceptInSameBlock() (gas: 2942393)
[PASS] testRageQuit_cannotReenter() (gas: 1019412)
[PASS] testRageQuit_ifNotOwner_butAuthority() (gas: 2655345)
[PASS] testRageQuit_multiple() (gas: 3116513)
[PASS] testRageQuit_revertIfBelowMinWithdrawAmount() (gas: 2650128)
[PASS] testRageQuit_revertIfDuplicateTokens() (gas: 1889400)
[PASS] testRageQuit_revertsIfNotOwner() (gas: 2586414)
[PASS] testRageQuit_single() (gas: 2668991)

After gas value:
[PASS] testRageQuit_cannotQuitAndAcceptInSameBlock() (gas: 2942249)
[PASS] testRageQuit_cannotReenter() (gas: 1019340)
[PASS] testRageQuit_ifNotOwner_butAuthority() (gas: 2655201)
[PASS] testRageQuit_multiple() (gas: 3116081)
[PASS] testRageQuit_revertIfBelowMinWithdrawAmount() (gas: 2650020)
[PASS] testRageQuit_revertIfDuplicateTokens() (gas: 1889328)
[PASS] testRageQuit_revertsIfNotOwner() (gas: 2586270)
[PASS] testRageQuit_single() (gas: 2668847)


### Recommanded code 

```solidity

uint256 j = 0;
do {
    // Must be retrieved before burning the token.
    withdrawAmounts[i] +=
        (balance * getVotingPowerShareOf(tokenIds[j])) /
        1e18;
    j++;
} while (j < tokenIds.length);

```

```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

379      for (uint i = 0; i < authoritiesLength - 1; ++i) {
            authorities[i] = opts.authorities[i];
        }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379-L381

```solidity
file: contracts/party/PartyGovernance.sol

305   for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
            isHost[govOpts.hosts[i]] = true;
        }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L305

```solidity
file:  contracts/party/PartyGovernanceNFT.sol

102     for (uint256 i; i < authorities.length; ++i) {
                isAuthority[authorities[i]] = true;
            }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L102



## [G-05] Use the existing memory value to do the addition and  subtraction instead of using state variable


```solidity
file: contracts/party/PartyGovernanceNFT.sol

189   mintedVotingPower += votingPower_;

223   mintedVotingPower += votingPower;


```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L189

## [G-06] Do not cache immutable values

```solidity
file:  contracts/crowdfund/InitialETHCrowdfund.sol
 
288    IGateKeeper _gateKeeper = gateKeeper;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L288


Before Gas value:
[PASS] test_contributeFor_doesNotUpdateExistingDelegation() (gas: 937909)
[PASS] test_contributeFor_works() (gas: 936882)
[PASS] test_contribute_aboveMaxContribution() (gas: 710701)
[PASS] test_contribute_aboveMaxTotalContribution() (gas: 926001)
[PASS] test_contribute_aboveMaxTotalContributionWhenWhenContributionBelowMinContributionAfterRefund() (gas: 954383)
[PASS] test_contribute_afterLost() (gas: 712671)
[PASS] test_contribute_belowMinContribution() (gas: 731452)
[PASS] test_contribute_gatekeeperChecksSender() (gas: 1502937)
[PASS] test_contribute_increaseVotingPowerToExistingCard() (gas: 912256)
[PASS] test_contribute_mintNewCard() (gas: 885805)
[PASS] test_contribute_mintNewCard_withDisableContributingForExistingCard() (gas: 711720)
[PASS] test_contribute_noContribution() (gas: 704426)
[PASS] test_contribute_noVotingPower() (gas: 711508)
[PASS] test_contribute_smallAmountWithFundingSplit() (gas: 903662)
[PASS] test_contribute_withFundingSplit() (gas: 903640)

After Gas value:
[PASS] test_contributeFor_doesNotUpdateExistingDelegation() (gas: 937896)
[PASS] test_contributeFor_works() (gas: 936877)
[PASS] test_contribute_aboveMaxContribution() (gas: 710698)
[PASS] test_contribute_aboveMaxTotalContribution() (gas: 925996)
[PASS] test_contribute_aboveMaxTotalContributionWhenWhenContributionBelowMinContributionAfterRefund() (gas: 954375)
[PASS] test_contribute_afterLost() (gas: 712668)
[PASS] test_contribute_belowMinContribution() (gas: 731449)
[PASS] test_contribute_gatekeeperChecksSender() (gas: 1502942)
[PASS] test_contribute_increaseVotingPowerToExistingCard() (gas: 912246)
[PASS] test_contribute_mintNewCard() (gas: 885800)
[PASS] test_contribute_mintNewCard_withDisableContributingForExistingCard() (gas: 711717)
[PASS] test_contribute_noContribution() (gas: 704418)
[PASS] test_contribute_noVotingPower() (gas: 711505)
[PASS] test_contribute_smallAmountWithFundingSplit() (gas: 903657)
[PASS] test_contribute_withFundingSplit() (gas: 903635)


## [G-07] Possible Optimizations in PartyGovernance.sol

General Optimization
Reducing the number of state variable updates can save gas. In Ethereum, every storage operation costs a significant amount of gas. Therefore, optimizing the contract to minimize storage operations can lead to substantial gas savings.

### Possible Optimization in function accept()

In the accept functions, the hasVoted mapping is updated twice. This can be optimized to a single update, which would save gas.


```solidity
file:  contracts/party/PartyGovernance.sol

626          // Cannot vote twice.
        if (info.hasVoted[msg.sender]) {
            revert AlreadyVotedError(msg.sender);
        }
        // Mark the caller as having voted.
        info.hasVoted[msg.sender] = true;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L626-L631

Estimated gas saved:
This optimization reduces the number of storage operations from 2 to 1. Given that a storage operation costs 20,000 gas, this optimization could save approximately 20,000 gas per call to these functions.


### Possible Optimization in function abdicateHost()

In the abdicateHost functions, the isHost mapping is updated twice. This can be optimized to a single update, which would save gas.


```solidity
file:  contracts/party/PartyGovernance.sol

457       function abdicateHost(address newPartyHost) external {
        _assertHost();
        // 0 is a special case burn address.
        if (newPartyHost != address(0)) {
            // Cannot transfer host status to an existing host.
            if (isHost[newPartyHost]) {
                revert InvalidNewHostError();
            }
            isHost[newPartyHost] = true;
        } else {
            // Burned the host status
            --numHosts;
        }
        isHost[msg.sender] = false;
        emit HostStatusTransferred(msg.sender, newPartyHost);
    }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L457-L472 

Estimated gas saved:
This optimization reduces the number of storage operations from 2 to 1. Given that a storage operation costs 20,000 gas, this optimization could save approximately 20,000 gas per call to these functions.

### Possible Optimization in function _adjustVotingPower()

In the _adjustVotingPower functions, the delegationsByVoter mapping is updated twice. This can be optimized to a single update, which would save gas.

```solidity
file: contracts/party/PartyGovernance.sol

944   function _adjustVotingPower(address voter, int192 votingPower, address delegate) internal {
        VotingPowerSnapshot memory oldSnap = _getLastVotingPowerSnapshotForVoter(voter);
        address oldDelegate = delegationsByVoter[voter];
        // If `oldDelegate` is zero and `voter` never delegated, then have
        // `voter` delegate to themself.
        oldDelegate = oldDelegate == address(0) ? voter : oldDelegate;
        // If the new `delegate` is zero, use the current (old) delegate.
        delegate = delegate == address(0) ? oldDelegate : delegate;

        VotingPowerSnapshot memory newSnap = VotingPowerSnapshot({
            timestamp: uint40(block.timestamp),
            delegatedVotingPower: oldSnap.delegatedVotingPower,
            intrinsicVotingPower: (oldSnap.intrinsicVotingPower.safeCastUint96ToInt192() +
                votingPower).safeCastInt192ToUint96(),
            isDelegated: delegate != voter
        });
        _insertVotingPowerSnapshot(voter, newSnap);
        delegationsByVoter[voter] = delegate;

        // This event is emitted even if the delegate did not change.
        emit PartyDelegateUpdated(voter, delegate);

        // Handle rebalancing delegates.
        _rebalanceDelegates(voter, oldDelegate, delegate, oldSnap, newSnap);
    }

```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L944-L968

Estimated gas saved:
This optimization reduces the number of storage operations from 2 to 1, Given that a storage operation costs 20,000 gas, this optimization could save approximately 20,000 gas per call to these functions.

### Possible Optimization in function propose()

In the propose functions, the _proposalStateByProposalId mapping is updated twice. This can be optimized to a single update, which would save gas.

```solidity
file:  contracts/party/PartyGovernance.sol

553       function propose(
        Proposal memory proposal,
        uint256 latestSnapIndex
    ) external returns (uint256 proposalId) {
        _assertActiveMember();
        proposalId = ++lastProposalId;
        // Store the time the proposal was created and the proposal hash.
        (
            _proposalStateByProposalId[proposalId].values,
            _proposalStateByProposalId[proposalId].hash
        ) = (

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L553-L563

Estimated gas saved:
This optimization reduces the number of storage operations from 2 to 1. Given that a storage operation costs 20,000 gas, this optimization could save approximately 20,000 gas per call to these functions.

### Possible Optimization

In the increaseVotingPower and _burnAndUpdateVotingPower functions, the votingPowerByTokenId mapping is updated twice. This can be optimized to a single update, which would save gas.

```solidity
file:  contracts/party/PartyGovernanceNFT.sol

208       function increaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        _assertAuthority();
        uint96 mintedVotingPower_ = mintedVotingPower;
        uint96 totalVotingPower = _getSharedProposalStorage().governanceValues.totalVotingPower;

        // Cap voting power to remaining unminted voting power supply. Allow
        // minting past total voting power if minting party cards for initial
        // crowdfund when there is no total voting power.
        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower) {
            unchecked {
                votingPower = totalVotingPower - mintedVotingPower_;
            }
        }

        // Update state.
        mintedVotingPower += votingPower;
        uint256 newIntrinsicVotingPower = votingPowerByTokenId[tokenId] + votingPower;
        votingPowerByTokenId[tokenId] = newIntrinsicVotingPower;

        emit PartyCardIntrinsicVotingPowerSet(tokenId, newIntrinsicVotingPower);

        _adjustVotingPower(ownerOf(tokenId), votingPower.safeCastUint96ToInt192(), address(0));
    }

268      function _burnAndUpdateVotingPower(
        uint256[] memory tokenIds,
        bool checkIfAuthorizedToBurn
    ) private returns (uint96 totalVotingPowerBurned) {
        for (uint256 i; i < tokenIds.length; ++i) {
            uint256 tokenId = tokenIds[i];
            address owner = ownerOf(tokenId);

            // Check if caller is authorized to burn the token.
            if (checkIfAuthorizedToBurn) {
                if (
                    msg.sender != owner &&
                    getApproved[tokenId] != msg.sender &&
                    !isApprovedForAll[owner][msg.sender]
                ) {
                    revert NotAuthorized();
                }
            }

            // Must be retrieved before updating voting power for token to be burned.
            uint96 votingPower = votingPowerByTokenId[tokenId].safeCastUint256ToUint96();

            totalVotingPowerBurned += votingPower;

            // Update voting power for token to be burned.
            delete votingPowerByTokenId[tokenId];
            emit PartyCardIntrinsicVotingPowerSet(tokenId, 0);
            _adjustVotingPower(owner, -votingPower.safeCastUint96ToInt192(), address(0));

            // Burn token.
            _burn(tokenId);

            emit Burn(msg.sender, tokenId, votingPower);
        }

        // Update minted voting power.
        mintedVotingPower -= totalVotingPowerBurned;
    }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L208-L230

Estimated gas saved:
This optimization reduces the number of storage operations from 2 to 1 in each of the increaseVotingPower and _burnAndUpdateVotingPower functions. Given that a storage operation costs 20,000 gas, this optimization could save approximately 20,000 gas per call to these functions.

### Possible Optimization

In the setSigningThresholdBps functions, the signingThersholdBps mapping is updated twice. This can be optimized to a single update, which would save gas.

```solidity
file:   contracts/signature-validators/OffChainSignatureValidator.sol

84     function setSigningThresholdBps(uint96 thresholdBps) external {
        Party party = Party(payable(msg.sender));
        emit SigningThresholdBpsSet(party, signingThersholdBps[party], thresholdBps);
        signingThersholdBps[party] = thresholdBps;
    }
}

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L84-L88

Estimated gas saved:
This optimization reduces the number of storage operations from 2 to 1. Given that a storage operation costs 20,000 gas, this optimization could save approximately 20,000 gas per call to these functions.

## [G-08] Possible Optimization in PartyGovernance.sol


If the precious list is not correct based on the inputs preciousTokens and preciousTokenIds, it is advisable to perform the check at the beginning of the function. This check ensures that if the precious list is invalid, all operations will be reverted. By placing the check for the validity of the precious list at the beginning of the function, more gas can be saved.

```solidity
file:  contracts/party/PartyGovernance.sol

740      if (!_isPreciousListCorrect(preciousTokens, preciousTokenIds)) {
            revert BadPreciousListError();
        }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L740-L742

If the number of hosts in govOpts exceeds the maximum value of uint8, it is recommended to perform the check at the beginning of the function. This check ensures that if the party host is invalid, all operations will be reverted. By placing the revert check at the beginning of the function, more gas can be saved in case of a revert.

```solidity
file:  contracts/party/PartyGovernance.sol

302    if (govOpts.hosts.length > type(uint8).max) {
            revert TooManyHosts();
        }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L302-L304

If the last rage quit timestamp is equal to the current block timestamp, it is advisable to perform the check at the beginning of the function. This check ensures that if the timestamp is invalid, all operations will be reverted. By placing the block timestamp check at the beginning of the function, more gas can be saved in case of a revert.

```solidity
file:    contracts/party/PartyGovernance.sol
 
622     if (lastRageQuitTimestamp == block.timestamp) {
            revert CannotRageQuitAndAcceptError();
        }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L622-L624


## [G-09] Possible Optimizations in PartyGovernance.sol

### Possible Optimization 1

In the execute and cancel function, there are multiple calls to  ProposalState storage proposalState = _proposalStateByProposalId[proposalId]; and ProposalState storage proposalState = _proposalStateByProposalId[proposalId];. These calls could be optimized by declaring them once at the beginning of the function and reusing the reference. This would save the gas used by the function call.

```solidity
file:  contracts/party/PartyGovernance.sol

706      function execute(
        uint256 proposalId,
        Proposal memory proposal,
        IERC721[] memory preciousTokens,
        uint256[] memory preciousTokenIds,
        bytes calldata progressData,
        bytes calldata extraData
    ) external payable {
        _assertNotGloballyDisabled();
        _assertActiveMember();
        // Get information about the proposal.
        ProposalState storage proposalState = _proposalStateByProposalId[proposalId];
        // Proposal details must remain the same from `propose()`.
        _validateProposalHash(proposal, proposalState.hash);
        ProposalStateValues memory values = proposalState.values;
        ProposalStatus status = _getProposalStatus(values);
        // The proposal must be executable or have already been executed but still
        // has more steps to go.
        if (status != ProposalStatus.Ready && status != ProposalStatus.InProgress) {
            revert BadProposalStatusError(status);
        }
        if (status == ProposalStatus.Ready) {
            // If the proposal has not been executed yet, make sure it hasn't
            // expired. Note that proposals that have been executed
            // (but still have more steps) ignore `maxExecutableTime`.
            if (proposal.maxExecutableTime < block.timestamp) {
                revert ExecutionTimeExceededError(
                    proposal.maxExecutableTime,
                    uint40(block.timestamp)
                );
            }
            proposalState.values.executedTime = uint40(block.timestamp);
        }
        // Check that the precious list is valid.
        if (!_isPreciousListCorrect(preciousTokens, preciousTokenIds)) {
            revert BadPreciousListError();
        }
        // Preemptively set the proposal to completed to avoid it being executed
        // again in a deeper call.
        proposalState.values.completedTime = uint40(block.timestamp);
        // Execute the proposal.
        bool completed = _executeProposal(
            proposalId,
            proposal,
            preciousTokens,
            preciousTokenIds,
            _getProposalFlags(values),
            progressData,
            extraData
        );
        if (!completed) {
            // Proposal did not complete.
            proposalState.values.completedTime = 0;
        }
    }

771      function cancel(uint256 proposalId, Proposal calldata proposal) external {
        _assertActiveMember();
        // Get information about the proposal.
        ProposalState storage proposalState = _proposalStateByProposalId[proposalId];
        // Proposal details must remain the same from `propose()`.
        _validateProposalHash(proposal, proposalState.hash);
        ProposalStateValues memory values = proposalState.values;
        {
            // Must be `InProgress`.
            ProposalStatus status = _getProposalStatus(values);
            if (status != ProposalStatus.InProgress) {
                revert BadProposalStatusError(status);
            }
        }
        {
            // Limit the `cancelDelay` to the global max and min cancel delay
            // to mitigate parties accidentally getting stuck forever by setting an
            // unrealistic `cancelDelay` or being reckless with too low a
            // cancel delay.
            uint256 cancelDelay = proposal.cancelDelay;
            uint256 globalMaxCancelDelay = _GLOBALS.getUint256(
                LibGlobals.GLOBAL_PROPOSAL_MAX_CANCEL_DURATION
            );
            uint256 globalMinCancelDelay = _GLOBALS.getUint256(
                LibGlobals.GLOBAL_PROPOSAL_MIN_CANCEL_DURATION
            );
            if (globalMaxCancelDelay != 0) {
                // Only if we have one set.
                if (cancelDelay > globalMaxCancelDelay) {
                    cancelDelay = globalMaxCancelDelay;
                }
            }
            if (globalMinCancelDelay != 0) {
                // Only if we have one set.
                if (cancelDelay < globalMinCancelDelay) {
                    cancelDelay = globalMinCancelDelay;
                }
            }
            uint256 cancelTime = values.executedTime + cancelDelay;
            // Must not be too early.
            if (block.timestamp < cancelTime) {
                revert ProposalCannotBeCancelledYetError(
                    uint40(block.timestamp),
                    uint40(cancelTime)
                );
            }
        }
        // Mark the proposal as cancelled by setting the completed time to the current
        // time with the high bit set.
        proposalState.values.completedTime = uint40(block.timestamp | UINT40_HIGH_BIT);
        {
            // Delegatecall into the proposal engine impl to perform the cancel.
            (bool success, bytes memory resultData) = (
                address(_getSharedProposalStorage().engineImpl)
            ).delegatecall(abi.encodeCall(IProposalExecutionEngine.cancelProposal, (proposalId)));
            if (!success) {
                resultData.rawRevert();
            }
        }
        emit ProposalCancelled(proposalId);
        // Notify third-party platforms that the governance NFT metadata has
        // updated for all tokens.
        emit BatchMetadataUpdate(0, type(uint256).max);
    }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L706-L760

Estimated gas saved:
This optimization could save around 1000 to 2000 gas per transaction, depending on the gas cost of the function call.


### Possible Optimization 2

In the _getVotingPowerSnapshotAt and _insertVotingPowerSnapshot function, there are multiple calls to   VotingPowerSnapshot[] storage snaps = _votingPowerSnapshotsByVoter[voter]; and VotingPowerSnapshot[] storage voterSnaps = _votingPowerSnapshotsByVoter[voter];. These calls could be optimized by declaring them once at the beginning of the function and reusing the reference. This would save the gas used by the function call.

```solidity
file:  contracts/party/PartyGovernance.sol

904      function _getVotingPowerSnapshotAt(
        address voter,
        uint40 timestamp,
        uint256 hintIndex
    ) internal view returns (VotingPowerSnapshot memory snap) {
        VotingPowerSnapshot[] storage snaps = _votingPowerSnapshotsByVoter[voter];
        uint256 snapsLength = snaps.length;
        if (snapsLength != 0) {
            if (
                // Hint is within bounds.
                hintIndex < snapsLength &&
                // Snapshot is not too recent.
                snaps[hintIndex].timestamp <= timestamp &&
                // Snapshot is not too old.
                (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)
            ) {
                return snaps[hintIndex];
            }

            // Hint was wrong, fallback to binary search to find snapshot.
            hintIndex = findVotingPowerSnapshotIndex(voter, timestamp);
            // Check that snapshot was found.
            if (hintIndex != type(uint256).max) {
                return snaps[hintIndex];
            }
        }

1021      function _insertVotingPowerSnapshot(address voter, VotingPowerSnapshot memory snap) private {
        emit PartyVotingSnapshotCreated(
            voter,
            snap.timestamp,
            snap.delegatedVotingPower,
            snap.intrinsicVotingPower,
            snap.isDelegated
        );

        VotingPowerSnapshot[] storage voterSnaps = _votingPowerSnapshotsByVoter[voter];
        uint256 n = voterSnaps.length;
        // If same timestamp as last entry, overwrite the last snapshot, otherwise append.
        if (n != 0) {
            VotingPowerSnapshot memory lastSnap = voterSnaps[n - 1];
            if (lastSnap.timestamp == snap.timestamp) {
                voterSnaps[n - 1] = snap;
                return;
            }
        }
        voterSnaps.push(snap);
    }
    

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L904-L929

Estimated gas saved:
This optimization could save around 1000 to 2000 gas per transaction, depending on the gas cost of the function call.

## [G-10] Pack structs by putting data types that can fit together next to each other

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot. This saves gas when writing to storage ~20000 gas

```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

26  struct InitialETHCrowdfundOptions {
        address payable initialContributor;
        address initialDelegate;
        uint96 minContribution;
        uint96 maxContribution;
        bool disableContributingForExistingCard;
        uint96 minTotalContributions;
        uint96 maxTotalContributions;
        uint16 exchangeRateBps;
        uint16 fundingSplitBps;
        address payable fundingSplitRecipient;
        uint40 duration;
        IGateKeeper gateKeeper;
        bytes12 gateKeeperId;
    }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L26-L40

## [G-11] The function should first verify if msg.value > 0 before performing any other operation (Saves 7146 Gas)

In case of a revert on msg.value == 0 we save 7146 gas on average from the protocol gas tests (to simulate the sad path ie msg.value == 0 see the test modification).

```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

255    function batchContributeFor(
        BatchContributeForArgs calldata args
    ) external payable onlyDelegateCall returns (uint96[] memory votingPowers) {
        votingPowers = new uint96[](args.recipients.length);
        uint256 valuesSum;
        for (uint256 i; i < args.recipients.length; ++i) {
            votingPowers[i] = _contribute(
                args.recipients[i],
                args.initialDelegates[i],
                args.values[i],
                args.tokenIds[i],
                args.gateDatas[i]
            );
            valuesSum += args.values[i];
        }
        if (msg.value != valuesSum) {
            revert InvalidMessageValue();
        }
    }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L255-L273

## [G-12] Don’t cache calls that are only used once

PartyGovernance.sol: Saves 20 gas

```solidity
file: contracts/party/PartyGovernance.sol

233   address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);

1115  uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L233

## [G-13] public functions not called by the contract should be declared external instead

when a function is declared as public, it is generated with an internal and an external interface. This means the function can be called both internally (within the contract) and externally (by other contracts or accounts). However, if a public function is never called internally and is only expected to be invoked externally, it is more gas-efficient to explicitly declare it as external.


```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

164     function contribute(
           address delegate,
           bytes memory gateData
       ) public payable onlyDelegateCall returns (uint96 votingPower) {

185     function contribute(
           uint256 tokenId,
           address delegate,
           bytes memory gateData
       ) public payable onlyDelegateCall returns (uint96 votingPower) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L166-L167


```solidity
file:   contracts/party/PartyGovernance.sol

333    function supportsInterface(bytes4 interfaceId) public pure virtual returns (bool) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L333

```solidity
file: contracts/party/PartyGovernanceNFT.sol

113    function supportsInterface(
          bytes4 interfaceId
      ) public pure override(PartyGovernance, ERC721, IERC165) returns (bool) {

123    function tokenURI(uint256) public view override returns (string memory) {

451    function transferFrom(address owner, address to, uint256 tokenId) public override {

458    function safeTransferFrom(address owner, address to, uint256 tokenId) public override {

465     function safeTransferFrom(
           address owner,
           address to,
           uint256 tokenId,
           bytes calldata data
       ) public override {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L113-L115

## [G-14] State variables which are not modified within functions should be set as constant or immutable for values set at deployment

```silidity
file: contracts/utils/Implementation.sol

15   bool public initialized;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol#L15

## [G-15] Use uint256(1)/uint256(2) instead for true and false boolean states

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. see source:

```solidity
file: contracts/utils/Implementation.sol

15   bool public initialized;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol#L15

```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

31   bool disableContributingForExistingCard;

352  bool revertOnFailure

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L31


```solidity
file: contracts/party/PartyGovernance.sol

96    bool isDelegated;

165   bool isDelegated

194   bool public emergencyExecuteDisabled;

747   bool completed = _executeProposal(

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L96


```solidity
file: contracts/party/PartyGovernanceNFT.sol

270   bool checkIfAuthorizedToBurn

353   bool isAuthority_ = isAuthority[msg.sender];

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L270


```solidity
file: contracts/proposals/ProposalStorage.sol

29   bool enableAddAuthorityProposal;

32   bool allowArbCallsToSpendPartyEth;

34   bool allowOperators;

36   bool distributionsRequireVote;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L29

## [G-16] Avoid Unnecessary Public Variables

Public storage variables increase the contract's size due to the implicit generation of public getter functions. This makes the contract larger and could increase deployment and interaction costs.

If you do not require other contracts to read these variables, consider making them private or internal.

```solidity
file:  contracts/party/PartyGovernance.sol
 
194   bool public emergencyExecuteDisabled;

196   uint16 public feeBps;

202   bytes32 public preciousListHash;

204   uint256 public lastProposalId;

206   mapping(address => bool) public isHost;

210   uint8 public numHosts;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L194

```solidity
file: contracts/party/PartyGovernanceNFT.sol

45    uint96 public tokenCount;

49    uint96 public mintedVotingPower;

55    uint40 public rageQuitTimestamp;

59    mapping(address => bool) public isAuthority;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L45

```solidity
file: contracts/utils/Implementation.sol

15    bool public initialized;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol#L15

## [G-17] Use constants instead of type(uintx).max

using type(uintx).max can result in higher gas costs because it involves a runtime operation to calculate the maximum value at runtime. This calculation is performed every time the expression is evaluated.

To save gas, it is recommended to use constants instead of type(uintx).max to represent the maximum value. By declaring a constant with the maximum value, the value is known at compile-time and does not require any runtime calculations.


```solidity
file: contracts/party/PartyGovernance.sol

187  uint96 private constant VETO_VALUE = type(uint96).max;

359  return getVotingPowerAt(voter, timestamp, type(uint256).max);

445  return high == 0 ? type(uint256).max : high - 1;

520  emit BatchMetadataUpdate(0, type(uint256).max);

581  emit BatchMetadataUpdate(0, type(uint256).max);

655  emit BatchMetadataUpdate(0, type(uint256).max);

688  emit BatchMetadataUpdate(0, type(uint256).max);

833  emit BatchMetadataUpdate(0, type(uint256).max);

897  emit BatchMetadataUpdate(0, type(uint256).max);

926  if (hintIndex != type(uint256).max) {

1083  if (pv.votes == type(uint96).max) {

```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L187


```solidity
file: contracts/proposals/ProposalExecutionEngine.sol

185  stor.nextProgressDataHash = bytes32(type(uint256).max);

314  require(uint8(proposalType) <= uint8(type(ProposalType).max));

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L185

## [G-18] addition and subtraction  which won’t underflow can be unchecked

```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

377   uint256 authoritiesLength = opts.authorities.length + 1;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L377


```solidity
file:  contracts/party/PartyGovernance.sol

374   return (snap.isDelegated ? 0 : snap.intrinsicVotingPower) + snap.delegatedVotingPower;

434   uint256 mid = (low + high) / 2;

440   low = mid + 1;

809   uint256 cancelTime = values.executedTime + cancelDelay;

918   (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)

1090  if (pv.passedTime + gv.executionDelay <= t) {

1105  if (pv.proposedTime + gv.voteDuration <= t) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L374


```solidity
file: contracts/party/PartyGovernanceNFT.sol

224   uint256 newIntrinsicVotingPower = votingPowerByTokenId[tokenId] + votingPower;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L224


```solidity
file:  contracts/crowdfund/InitialETHCrowdfund.sol

382    authorities[authoritiesLength - 1] = address(this);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L382


```solidity
file:  contracts/party/PartyGovernance.sol

445   return high == 0 ? type(uint256).max : high - 1;

634   uint96 votingPower = getVotingPowerAt(msg.sender, values.proposedTime - 1, snapIndex);

918   (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)

1034  VotingPowerSnapshot memory lastSnap = voterSnaps[n - 1];

1036   voterSnaps[n - 1] = snap;

1049   snap = voterSnaps[n - 1];

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L445



## [G-19] Use assembly in place of abi.decode to extract calldata values more efficiently

Instead of using abi.decode, we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use.

```solidity
file:  contracts/party/PartyGovernance.sol

892    nextProgressData = abi.decode(resultData, (bytes));

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L892

```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

367   amounts[i] = abi.decode(r, (uint96));

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L367


```solidity
file: contracts/proposals/ProposalExecutionEngine.sol

133   ProposalEngineOpts memory opts = abi.decode(initializeData, (ProposalEngineOpts));

319   (address expectedImpl, bytes memory initData) = abi.decode(proposalData, (address, bytes));

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L133

```solidity
file: contracts/proposals/SetGovernanceParameterProposal.sol

28    SetGovernanceParameterProposalData memory proposalData = abi.decode(

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol#L28


```solidity
file: contracts/proposals/SetSignatureValidatorProposal.sol

31    SetSignatureValidatorProposalData memory data = abi.decode(

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L31

## [G-20] Cache external calls outside of loop to avoid re-calling function on each iteration

Performing STATICCALLs that do not depend on variables incremented in loops should always try to be avoided within the loop. In the following instances, we are able to cache the external calls outside of the loop to save a STATICCALL (100 gas) per loop iteration.


### Cache token.compatTransfer(feeRecipient, fee); and token.compatTransfer(receiver, amount); outside of loop to save 2 STATICCALL per loop iteration

```slidity
file:  contracts/party/PartyGovernanceNFT.sol

408               for (uint256 i; i < withdrawTokens.length; ++i) {
                IERC20 token = withdrawTokens[i];
                uint256 amount = withdrawAmounts[i];

                // Take fee from amount.
                uint256 fee = (amount * feeBps_) / 1e4;

                if (fee > 0) {
                    amount -= fee;

                    // Transfer fee to fee recipient.
                    if (address(token) == ETH_ADDRESS) {
                        payable(feeRecipient).transferEth(fee);
                    } else {
                        token.compatTransfer(feeRecipient, fee);
                    }
                }

                if (amount > 0) {
                    uint256 minAmount = minWithdrawAmounts[i];

                    // Check amount is at least minimum.
                    if (amount < minAmount) {
                        revert BelowMinWithdrawAmountError(amount, minAmount);
                    }

                    // Transfer token from party to recipient.
                    if (address(token) == ETH_ADDRESS) {
                        payable(receiver).transferEth(amount);
                    } else {
                        token.compatTransfer(receiver, amount);
                    }
                }
            }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L408-L441

## [G-21] Use assembly to perform efficient back-to-back calls

If similar external calls are performed back-to-back, we can use assembly to reuse any function signatures and function parameters that stay the same. In addition, we can also reuse the same memory space for each function call (scratch space + free memory pointer), which can potentially allow us to avoid memory expansion costs. In this case, we are also able to efficiently store the function signatures together in memory as one word, saving multiple MLOADs in the process.

Note: In order to do this optimization safely we will cache the free memory pointer value and restore it once we are done with our function calls. We will also set the zero slot back to 0 if neccessary.

### Use for _GLOBALS.getUint256() back to back external call assembly

```solidity
file:  contracts/party/PartyGovernance.sol

791      uint256 globalMaxCancelDelay = _GLOBALS.getUint256(
            LibGlobals.GLOBAL_PROPOSAL_MAX_CANCEL_DURATION
        );
        uint256 globalMinCancelDelay = _GLOBALS.getUint256(
            LibGlobals.GLOBAL_PROPOSAL_MIN_CANCEL_DURATION
        );
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L791-L796

## [G-22] Use hardcoded address instead of address(this)

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this. Refrences


```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

358   (bool s, bytes memory r) = address(this).call(

382   authorities[authoritiesLength - 1] = address(this);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L358


```solidity
file:  contracts/party/PartyGovernance.sol

496    if (msg.sender != address(this)) {

527    Party(payable(address(this))),

538    Party(payable(address(this))),

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L496


```solidity
file: contracts/party/PartyGovernanceNFT.sol

68    if (msg.sender != address(this)) {

387   ? address(this).balance

388   : withdrawTokens[i].balanceOf(address(this));

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L68


```solidity
file:  contracts/utils/Implementation.sol

18    implementation = address(this);

23    if (address(this) == implementation) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol#L18


## [G-23] Use assembly for loops

In the following instances, assembly is used for more gas efficient loops. The only memory slots that are manually used in the loops are scratch space (0x00-0x20), the free memory pointer (0x40), and the zero slot (0x60). This allows us to avoid using the free memory pointer to allocate new memory, which may result in memory expansion costs.

Note that in order to do this optimization safely we will need to cache and restore the free memory pointer after the loop. We will also set the zero slot (0x60) back to 0.

```solidity
file:  contracts/crowdfund/InitialETHCrowdfund.sol

379    for (uint i = 0; i < authoritiesLength - 1; ++i) {
            authorities[i] = opts.authorities[i];
        }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379-L381


```solidity
file: contracts/party/PartyGovernance.sol

305   for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
            isHost[govOpts.hosts[i]] = true;
        }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L305-L307


```solidity 
file:  contracts/party/PartyGovernanceNFT.sol

102    for (uint256 i; i < authorities.length; ++i) {
                isAuthority[authorities[i]] = true;
            }

391   for (uint256 j; j < tokenIds.length; ++j) {
                    // Must be retrieved before burning the token.
                    withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
                }

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L102-L104

## [G-24] Amounts should be checked for 0 before calling a transfer

Checking non-zero transfer values can avoid an expensive external call and save gas. While this is done at some places, it’s not consistently done in the solution. I suggest adding a non-zero-value

```solidity
file: contracts/party/PartyGovernance.sol

534   IERC20(token).compatTransfer(address(distributor), amount);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L534

```solidity
file:  contracts/party/PartyGovernanceNFT.sol

436    payable(receiver).transferEth(amount);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L436

## [G-25] Empty blocks should be removed or emit something

f the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if( x ) {}else if(y){ . . . }else{ . . . } => if ( !x) {if(y) { . . . }else{ . . .}}).
Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

99    constructor(IGlobals globals) ETHCrowdfundBase(globals) {}

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L99

## [G-26] Use Assembly To Check For address(0)

It’s generally more gas-efficient to use assembly to check for a zero address (address(0)) than to use the == operator.

The reason for this is that the == operator generates additional instructions in the EVM bytecode, which can increase the gas cost of your contract. By using assembly, you can perform the zero address check more efficiently and reduce the overall gas cost of your contract.

Here’s an example of how you can use assembly to check for a zero address:

```solidity
contract MyContract {
    function isZeroAddress(address addr) public pure returns (bool) {
        uint256 addrInt = uint256(addr);
        
        assembly {
            // Load the zero address into memory
            let zero := mload(0x00)
            
            // Compare the address to the zero address
            let isZero := eq(addrInt, zero)
            
            // Return the result
            mstore(0x00, isZero)
            return(0, 0x20)
        }
    }
}
```

In the above example, we have a function isZeroAddress that takes an address as input and returns a boolean value, indicating whether the address is equal to the zero address. Inside the function, we convert the address to an integer using uint256(addr), and then use assembly to compare the integer to the zero address.

By using assembly to perform the zero address check, we can make our code more gas-efficient and reduce the overall cost of our contract. It’s important to note that assembly can be more difficult to read and maintain than Solidity code, so it should be used with caution and only when necessary

```solidity
file: contracts/crowdfund/InitialETHCrowdfund.sol

283    if (delegate == address(0)) {

289    if (_gateKeeper != IGateKeeper(address(0))) {

384    if (address(customMetadataProvider) == address(0)) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L283

```solidity
file: contracts/party/PartyGovernance.sol

460   if (newPartyHost != address(0)) {

939   _adjustVotingPower(from, -powerI192, address(0));

940   _adjustVotingPower(to, powerI192, address(0));

949   oldDelegate = oldDelegate == address(0) ? voter : oldDelegate;

951   delegate = delegate == address(0) ? oldDelegate : delegate;

979   if (newDelegate == address(0) || oldDelegate == address(0)) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L460

```solidity
file: contracts/party/PartyGovernanceNFT.sol

138   return (address(0), 0); // Just to make the compiler happy.

196   if (delegate_ != address(0)) {

229   _adjustVotingPower(ownerOf(tokenId), votingPower.safeCastUint96ToInt192(), address(0));

241   _adjustVotingPower(ownerOf(tokenId), -votingPower.safeCastUint96ToInt192(), address(0));

295   _adjustVotingPower(owner, -votingPower.safeCastUint96ToInt192(), address(0));

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L138

```solidity
file: contracts/proposals/ProposalExecutionEngine.sol

128   if (oldImpl != address(0)) return;

231   if (address(validator) != address(0)) {

234   if (tx.origin == address(0)) {

236   if (address(validator) == address(0)) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L128
