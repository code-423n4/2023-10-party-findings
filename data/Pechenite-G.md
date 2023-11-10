### [GAS-01] Utilizing Do-While Loops for Enhanced Gas Efficiency
Opting for `do-while` loops over `for` loops can yield increased gas efficiency.
Even when incorporating an `if` condition to cater to situations where the loop may not execute at all, a `do-while` loop can still prove to be more economical in terms of gas consumption.

Gas Conservation per Instance: Approximately 255 units (Total Savings: Approximately 2,550 units)

<details>
<summary><i>This issue manifests in 10 instances:</i></summary>

```solidity
📁 File: contracts/crowdfund/InitialETHCrowdfund.sol

211:         for (uint256 i; i < numContributions; ++i) { 

260:         for (uint256 i; i < args.recipients.length; ++i) { 

357:         for (uint256 i; i < numRefunds; ++i) { 

379:         for (uint i = 0; i < authoritiesLength - 1; ++i) { 
```
[211 - GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L211-L211), [260 - GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L260-L260), [357 - GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L357-L357), [379 - GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L379-L379)

```solidity
📁 File: contracts/party/PartyGovernance.sol

305:         for (uint256 i = 0; i < govOpts.hosts.length; ++i) { 
```
[GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol/#L305-L305)

```solidity
📁 File: contracts/party/PartyGovernanceNFT.sol

102:             for (uint256 i; i < authorities.length; ++i) { 

272:         for (uint256 i; i < tokenIds.length; ++i) { 

378:             for (uint256 i; i < withdrawTokens.length; ++i) { 

391:                 for (uint256 j; j < tokenIds.length; ++j) { 

408:             for (uint256 i; i < withdrawTokens.length; ++i) { 
```
[211- GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L102-L102), [272](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L272-L272), [378 - GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L378-L378), [391 - GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L391-L391), [408 - GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L408-L408)

</details>

---

### [GAS-02] Employing `emit` within a Loop
Issuing an event within a loop triggers a LOG operation N times, where N corresponds to the loop's length. Consider revising the code to emit the event only once upon loop completion. **The potential gas savings should be multiplied by the average loop length.**

Gas Savings per Instance: Approximately 375 units (Total Savings: Approximately 750 units)

<i>This issue is observed in 2 instances:</i>

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
[GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol/#L272-L301)

---

### [GAS-03] Opting for Countdown in `for` Statements for Enhanced Gas Efficiency
Utilizing countdown is more gas-efficient than counting up because it avoids the transition from a zero variable to a non-zero variable and also allows for gas refunds during the last transaction when transitioning from a non-zero variable to zero. [More information](https://solodit.xyz/issues/g-02-counting-down-in-for-statements-is-more-gas-efficient-code4rena-pooltogether-pooltogether-git)

Gas Conservation per Instance: Approximately 16 units (Total Savings: Approximately 160 units)

<details>
<summary><i>This issue arises in 10 instances:</i></summary>

```solidity
📁 File: contracts/crowdfund/InitialETHCrowdfund.sol

211:         for (uint256 i; i < numContributions; ++i) { 

260:         for (uint256 i; i < args.recipients.length; ++i) { 

357:         for (uint256 i; i < numRefunds; ++i) { 

379:         for (uint i = 0; i < authoritiesLength - 1; ++i) { 
```
[GitHub Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol/#L211-L211)