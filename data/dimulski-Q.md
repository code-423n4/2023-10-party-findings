# QA Report - Party Protocol Audit Contest | 31 Oct 2023 - 10 Nov 2023

---
## Low Issues

## [L-01] ```burn()``` can be called after ```InitialETHCrowdfund.sol``` is finalized and governance has started
```burn()``` is a function that can be called only by an authority and in turn calls ```_burnAndUpdateVotingPower()``` which substracts the ```votingPower``` that will be burnt from the ```mintedVotingPower``` which works fine if crowdfund is ongoing. However there is no check if a crowdfund is finalized and governance has started. If ```InitialETHCrowdfund.sol``` is finalized, and governance has started and  ```burn()``` is called the voting power of a certain tokenId will be decreased, and that token will be burnt, but the ```totalVotingPower``` will stay the same and thus prevent the party from reaching full consensus. Consider checking in ```_burnAndUpdateVotingPower()``` if governance has started and if so call ```decreaseTotalVotingPower()``` with the voting power of the burnt token as a parameter. Or revert the function call if governance has started.

```
function burn(uint256[] memory tokenIds) public {
        _assertAuthority();
        _burnAndUpdateVotingPower(tokenIds, false);
    }
```
which in turn calls: 
```
    function _burnAndUpdateVotingPower(
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

### GitHub Links: https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L263-L266

## [L-02] ```decreaseVotingPower()``` can be called after ```InitialETHCrowdfund.sol``` is finalized and governance has started
```decreaseVotingPower()``` is only callable by an authority for a certain tokenId, and substracts the ```votingPower``` from the ```mintedVotingPower``` which works fine if crowdfund is ongoing. However it can be called after a crowdfund is over for example after ```InitialETHCrowdfund.sol``` is finalized, and governance has started,  this can result in the party governance not being able to reach full consensus. If ```decreaseTotalVotingPower()``` is not called afterwards, or is called with a wrong ```votingPower```. Consider checking if governance has started and if so call ```decreaseTotalVotingPower()``` with the voting power that was substracted as a parameter. Or revert the function call if governance has started.

```
  function decreaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        _assertAuthority();
        mintedVotingPower -= votingPower;
        votingPowerByTokenId[tokenId] -= votingPower;

        _adjustVotingPower(ownerOf(tokenId), -votingPower.safeCastUint96ToInt192(), address(0));
    }
```
### GitHub Links: https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L236-L242
## Non Critical Issues
## [N-01] Consider using ```_assertActiveMember()```

In ```PartyGovernance.sol``` in ```distribute()``` consider replacing 

```
// Must be an active member.
            VotingPowerSnapshot memory snap = _getLastVotingPowerSnapshotForVoter(msg.sender);
            if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {
                revert NotAuthorized();
            }
```

with 
```
_assertActiveMember()
```

### GitHub Links: https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L487-L508

## [N-02] Consider adding ```_assertActiveMember()``` in ```accept()```

In ```PartyGovernance.sol``` in ```accept()``` consider adding ```_assertActiveMember()``` so if not an active member calls the accept function it will revert directly

```
function accept(uint256 proposalId, uint256 snapIndex) public returns (uint256 totalVotes) {
         +  _assertActiveMember();
           ...
}
```



### GitHub Links: https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L595-L658
