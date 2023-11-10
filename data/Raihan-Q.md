## [L-01] no check if the burn amount is zero or not
if the amount is zero so the unhecked block will divided zero on 3 and we use gas for nothing ! if we set zero we may pass the _burn checks, i know it is passed by only permit but it's better to avoid this happen because it's seting by human and it means it can be set with 0 balance to burn !



```solidity
File: contracts/party/PartyGovernanceNFT.sol
298   _burn(tokenId);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L298


## [L-02] Contributed emits wrong sender address
In the ETHCrowdfundBase._processContribution function the Contributed is emitted ([Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L210)).

It is defined as:
[Link](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L75-L80)

```solidity
    event Contributed(
        address indexed sender,
        address indexed contributor,
        uint256 amount,
        address delegate
    );
```

So the first parameter should be the sender address.

When the event is emitted the first parameter is msg.sender. The issue is that the sender address and msg.sender can be different. So the event in some cases contains wrong information.

`Fix code:`
```diff
- emit Contributed(msg.sender, contributor, amount, delegate);
+ emit Contributed(sender, contributor, amount, delegate);
```

```solidity
File: contracts/party/PartyGovernance.sol
471  emit HostStatusTransferred(msg.sender, newPartyHost);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L471


```solidity
File: contracts/party/PartyGovernanceNFT.sol
300  emit Burn(msg.sender, tokenId, votingPower);

447  emit RageQuit(msg.sender, tokenIds, withdrawTokens, receiver);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L300


## [L-03] AVOID USING TX.ORIGIN
tx.origin is a global variable in Solidity that returns the address of the account that sent the transaction.
Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.

```solidity
File: contracts/proposals/ProposalExecutionEngine.sol
234  if (tx.origin == address(0)) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L234


## [L-04] Condition will not revert when block.timestamp is == to the compared variable
The condition does not revert when block.timestamp is equal to the compared > or < variable. For example, if there is a check for block.timestamp > identifier then there should be a check for cases where block.timestamp is == to identifier

```solidity
File: contracts/party/PartyGovernance.sol
731   if (proposal.maxExecutableTime < block.timestamp) {

811   if (block.timestamp < cancelTime) {    
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L731


```solidity
File: contracts/party/PartyGovernanceNFT.sol
361   currentRageQuitTimestamp < block.timestamp
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L361


## [L‑05] Don’t use payable.transfer()/payable.send()
The use of payable.transfer() is heavily frowned upon because it can lead to the locking of funds. The transfer() call requires that the recipient is either an EOA account, or is a contract that has a payable callback. For the contract case, the transfer() call only provides 2300 gas for the contract to complete its operations. This means the following cases can cause the transfer to fail:
- The contract does not have a payable callback
- The contract’s payable callback spends more than 2300 gas (which is only enough to emit something)
- The contract is called through a proxy which itself uses up the 2300 gas Use OpenZeppelin’s Address.sendValue() instead
[Reffrence](https://code4rena.com/reports/2022-07-golom#l02--dont-use-payabletransferpayablesend)


```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
224  if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L224


## [L‑06] Void constructor
Calls to base contract constructors that are unimplemented leads to misplaced assumptions. Check if the constructor is implemented or remove call if not. (see [here](https://github.com/crytic/slither/wiki/Detector-Documentation#void-constructor))

```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
99    constructor(IGlobals globals) ETHCrowdfundBase(globals) {}
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L99