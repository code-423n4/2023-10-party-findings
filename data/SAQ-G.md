## Summary

### Gas Optimization

no | Issue |Instances||
|-|:-|:-:|:-:|
| [G-01] | Use fixed bytes instead of string | 1 | - |
| [G-02] | Before transfer of  some functions, we should check some variables for possible gas save | 1 | - |
| [G-03] | With assembly, .call (bool success)  transfer can be done gas-optimized | 2 | - |
| [G-04] | Use constants instead of type(uintx).max | 6 | - |
| [G-05] | Use nested if() instead of && | 5 | - |
| [G-06] | abi.encode() is less efficient than  abi.encodepacked() | 1 | - |
| [G-07] | Use hardcode address instead address(this) | 5 | - |
| [G-08] | Assign the msg.value to a varaible | 5 | - |
| [G-09] | Use assembly for math (add, sub, mul, div) | 4 | - |
| [G-10] | Using this to access functions results in an external call, wasting gas | 1 | - |
| [G-11] | No need to evaluate all expressions to know if one of them is true | 4 | - |
| [G-12] | Pre-increment and pre-decrement are cheaper than +1 ,-1 | 4 | - |
## Gas Optimizations  

## [G-01] Use fixed bytes instead of string

As a rule of thumb, use bytes for arbitrary-length raw byte data and string for arbitrary-length string (UTF-8) data.
If you can limit the length to a certain number of bytes, always use one of bytes1 to bytes32 because they are much cheaper.

```solidity
file: /contracts/crowdfund/InitialETHCrowdfund.sol

46        string symbol;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L46


## [G-02] Before transfer of  some functions, we should check some variables for possible gas save

Before transfer, we should check for amount being 0 so the function doesn't run when its not gonna do anything:

```solidity
file: /contracts/party/PartyGovernanceNFT.sol

454        super.transferFrom(owner, to, tokenId);

461        super.safeTransferFrom(owner, to, tokenId);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L454


## [G-03] With assembly, .call (bool success)  transfer can be done gas-optimized

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.

Did you know that the normal "(bool success, bytes memory returnData) = http://target.call()" automatically copies the return data to memory even if you omit the returnData variable? If a relayer executes transactions with such calls it can lead to a gas-griefing attack.

```solidity
file: /contracts/party/PartyGovernance.sol
 
823            (bool success, bytes memory resultData) = (
                address(_getSharedProposalStorage().engineImpl)
825            ).delegatecall(abi.encodeCall(IProposalExecutionEngine.cancelProposal, (proposalId)));


846        (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L823-L825


## [G-04] Use constants instead of type(uintx).max

type(uint120).max or type(uint128).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.

```solidity
file: /contracts/party/PartyGovernance.sol

302        if (govOpts.hosts.length > type(uint8).max) {

359        return getVotingPowerAt(voter, timestamp, type(uint256).max);

445        return high == 0 ? type(uint256).max : high - 1;

520        emit BatchMetadataUpdate(0, type(uint256).max);

926            if (hintIndex != type(uint256).max) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L302


```solidity
file: /contracts/proposals/ProposalExecutionEngine.sol

185            stor.nextProgressDataHash = bytes32(type(uint256).max);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L185


## [G-05] Use nested if() instead of &&

Using nested if is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

```solidity
file: /contracts/party/PartyGovernance.sol

255        if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {


606     if (
                status != ProposalStatus.Voting &&
                status != ProposalStatus.Passed &&
                status != ProposalStatus.Ready
610            ) {    


674            if (
                status != ProposalStatus.Voting &&
                status != ProposalStatus.Passed &&
                status != ProposalStatus.Ready
678            ) {


912            if (
                // Hint is within bounds.
                hintIndex < snapsLength &&
                // Snapshot is not too recent.
                snaps[hintIndex].timestamp <= timestamp &&
                // Snapshot is not too old.
                (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)
919            ) {    

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L225


```solidity
file: /contracts/party/PartyGovernanceNFT.sol

278            if (checkIfAuthorizedToBurn) {
                if (
                    msg.sender != owner &&
                    getApproved[tokenId] != msg.sender &&
                    !isApprovedForAll[owner][msg.sender]
282                ) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L278-L282


## [G-06] abi.encode() is less efficient than  abi.encodepacked()

In terms of efficiency, abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost of reduced safety, as abi.encodePacked() does not perform any type checking or padding of data.

```solidity
file: /contracts/party/PartyGovernance.sol

286            abi.encode(proposalEngineOpts)

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L286


## [G-07] Use hardcode address instead address(this)
 
 Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.
 if the contract's address is needed in the code, it's more gas-efficient to hardcode the address as a constant rather than using the address(this) expression. This is because using address(this) requires additional gas consumption to compute the contract's address at runtime.

an example :
```solidity
contract MyContract {
    address constant public CONTRACT_ADDRESS = 0x1234567890123456789012345678901234567890;
    
    function getContractAddress() public view returns (address) {
        return CONTRACT_ADDRESS;
    }
}
```
Instances:

```solidity
file: /contracts/party/PartyGovernance.sol

496        if (msg.sender != address(this)) {

527                    Party(payable(address(this))),   

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L496


```solidity
file: /contracts/crowdfund/InitialETHCrowdfund.sol

358            (bool s, bytes memory r) = address(this).call(

382        authorities[authoritiesLength - 1] = address(this);   

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L358


```solidity
file: /contracts/party/PartyGovernanceNFT.sol

68        if (msg.sender != address(this)) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L68

## [G-08] Assign the msg.value to a varaible

Assigning msg.value to another variable allows you to avoid repeated accesses to the msg.value special variable, which can potentially save gas. By assigning it to a local variable, you can reuse that variable throughout your function without incurring the cost of accessing msg.value multiple times.

```solidity
file: /contracts/crowdfund/InitialETHCrowdfund.sol

///@audit assign the ' msg.value ' to a variable and after we can use the variable multiple times.

140        uint96 initialContribution = msg.value.safeCastUint256ToUint96();

172                msg.value.safeCastUint256ToUint96(),

194                msg.value.safeCastUint256ToUint96(),

245                msg.value.safeCastUint256ToUint96(),

270        if (msg.value != valuesSum) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L140


## [G-09] Use assembly for math (add, sub, mul, div)

Use assembly for math instead of Solidity. You can check for overflow/underflow in assembly to ensure safety. If using Solidity versions < 0.8.0 and you are using Safemath, you can gain significant gas savings by using assembly to calculate values and checking for overflow/underflow.

```solidity
file: /contracts/party/PartyGovernance.sol

434            uint256 mid = (low + high) / 2;

440                low = mid + 1;

1115        uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L434


```solidity
file: /contracts/party/PartyGovernanceNFT.sol

413                uint256 fee = (amount * feeBps_) / 1e4;

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L413C


## [G-10] Using this to access functions results in an external call, wasting gas

External calls have an overhead of 100 gas, which can be avoided by not referencing the function using this. Contracts are allowed to override their parents' functions and change the visibility from external to public, so make this change if it's required in order to call the function internally.

```solidity
file: /contracts/crowdfund/InitialETHCrowdfund.sol

359                abi.encodeCall(this.refund, (tokenIds[i]))

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L359


## [G-11] No need to evaluate all expressions to know if one of them is true

When we have a code expressionA || expressionB if expressionA is true then expressionB will not be evaluated and gas saved

```solidity
file: /contracts/party/PartyGovernance.sol

314        if (
            functionSelector == ERC1155TokenReceiverBase.onERC1155BatchReceived.selector ||
            functionSelector == ERC1155TokenReceiverBase.onERC1155Received.selector ||
            functionSelector == IERC721Receiver.onERC721Received.selector
318        ) {



335            interfaceId == type(IERC721Receiver).interfaceId ||
336            interfaceId == type(ERC1155TokenReceiverBase).interfaceId ||


```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L314-L318


```solidity
file: /contracts/party/PartyGovernanceNFT.sol#
 
116        return
            PartyGovernance.supportsInterface(interfaceId) ||
            ERC721.supportsInterface(interfaceId) ||
            interfaceId == type(IERC2981).interfaceId;
120    }


359                if (
                    currentRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY ||
                    currentRageQuitTimestamp < block.timestamp
362                ) {

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L116-L120


## [G-12] Pre-increment and pre-decrement are cheaper than +1 ,-1

```solidity
file: /contracts/crowdfund/InitialETHCrowdfund.sol

377        uint256 authoritiesLength = opts.authorities.length + 1;

382        authorities[authoritiesLength - 1] = address(this);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L377


```solidity
file: /contracts/party/PartyGovernance.sol

918                (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)

634        uint96 votingPower = getVotingPowerAt(msg.sender, values.proposedTime - 1, snapIndex);

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L918

