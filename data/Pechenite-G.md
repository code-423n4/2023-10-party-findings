### [GAS-01] Do-While loops are cheaper than for loops
Using `do-while` loops instead of `for` loops can be more gas-efficient.
Even if you add an `if` condition to account for the case where the loop doesn't execute at all, a `do-while` loop can still be cheaper in terms of gas.


Gas saved per Instance: ~255 *(Total: ~2,550)*
<details>
<summary><i>There are 10 instances of this issue:</i></summary>

```solidity
📁 File: contracts/crowdfund/InitialETHCrowdfund.sol

211:         for (uint256 i; i < numContributions; ++i) { 

260:         for (uint256 i; i < args.recipients.length; ++i) { 

357:         for (uint256 i; i < numRefunds; ++i) { 

379:         for (uint i = 0; i < authoritiesLength - 1; ++i) { 
```
[211](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L211-L211), [260](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L260-L260), [357](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L357-L357), [379](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L379-L379)

```solidity
📁 File: contracts/party/PartyGovernance.sol

305:         for (uint256 i = 0; i < govOpts.hosts.length; ++i) { 
```
[305](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol/#L305-L305)

```solidity
📁 File: contracts/party/PartyGovernanceNFT.sol

102:             for (uint256 i; i < authorities.length; ++i) { 

272:         for (uint256 i; i < tokenIds.length; ++i) { 

378:             for (uint256 i; i < withdrawTokens.length; ++i) { 

391:                 for (uint256 j; j < tokenIds.length; ++j) { 

408:             for (uint256 i; i < withdrawTokens.length; ++i) { 
```
[102](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L102-L102), [272](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L272-L272), [378](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L378-L378), [391](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L391-L391), [408](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L408-L408)

</details>

---

### [GAS-02] Use of `emit` inside a loop
Emitting an event inside a loop performs a LOG op N times, where N is the loop length. Consider refactoring the code to emit the event only once at the end of loop. **Gas savings should be multiplied by the average loop length.**


Gas saved per Instance: ~375 *(Total: ~750)*

<i>There are 2 instaces of this issue:</i>

```solidity
📁 File: contracts/party/PartyGovernanceNFT.sol

/// @audit line 294
/// @audit line 300
272:         for (uint256 i; i < tokenIds.length; ++i) { 
273:             uint256 tokenId = tokenIds[i];
274:             address owner = ownerOf(tokenId);
275: 
276:             // Check if caller is authorized to burn the token.
277:             if (checkIfAuthorizedToBurn) {
278:                 if (
279:                     msg.sender != owner &&
280:                     getApproved[tokenId] != msg.sender &&
281:                     !isApprovedForAll[owner][msg.sender]
282:                 ) {
283:                     revert NotAuthorized();
284:                 }
285:             }
286: 
287:             // Must be retrieved before updating voting power for token to be burned.
288:             uint96 votingPower = votingPowerByTokenId[tokenId].safeCastUint256ToUint96();
289: 
290:             totalVotingPowerBurned += votingPower;
291: 
292:             // Update voting power for token to be burned.
293:             delete votingPowerByTokenId[tokenId];
294:             emit PartyCardIntrinsicVotingPowerSet(tokenId, 0);
295:             _adjustVotingPower(owner, -votingPower.safeCastUint96ToInt192(), address(0));
296: 
297:             // Burn token.
298:             _burn(tokenId);
299: 
300:             emit Burn(msg.sender, tokenId, votingPower);
301:         }
```
[272](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L272-L301)

---

### [GAS-03] Counting down in `for` statements is more gas efficient
Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable. [More info](https://solodit.xyz/issues/g-02-counting-down-in-for-statements-is-more-gas-efficient-code4rena-pooltogether-pooltogether-git)


Gas saved per Instance: ~16 *(Total: ~160)*
<details>
<summary><i>There are 10 instances of this issue:</i></summary>

```solidity
📁 File: contracts/crowdfund/InitialETHCrowdfund.sol

211:         for (uint256 i; i < numContributions; ++i) { 

260:         for (uint256 i; i < args.recipients.length; ++i) { 

357:         for (uint256 i; i < numRefunds; ++i) { 

379:         for (uint i = 0; i < authoritiesLength - 1; ++i) { 
```
[211](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L211-L211), [260](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L260-L260), [357](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L357-L357), [379](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L379-L379)

```solidity
📁 File: contracts/party/PartyGovernance.sol

305:         for (uint256 i = 0; i < govOpts.hosts.length; ++i) { 
```
[305](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol/#L305-L305)

```solidity
📁 File: contracts/party/PartyGovernanceNFT.sol

102:             for (uint256 i; i < authorities.length; ++i) { 

272:         for (uint256 i; i < tokenIds.length; ++i) { 

378:             for (uint256 i; i < withdrawTokens.length; ++i) { 

391:                 for (uint256 j; j < tokenIds.length; ++j) { 

408:             for (uint256 i; i < withdrawTokens.length; ++i) { 
```
[102](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L102-L102), [272](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L272-L272), [378](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L378-L378), [391](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L391-L391), [408](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L408-L408)

</details>

---

