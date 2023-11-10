# Low

## [L-01] Veto Period should also be skipped if there are no hosts
[PartyGovernance Line 1122](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1122)

**Issue Description**
The recent update to `PartyGovernance` introduces a new feature allowing the bypassing of the veto period if all hosts accept a proposal. To facilitate this, the `_hostsAccepted()` function was implemented. Presently, this function only returns true when the snapshot contains more than 0 hosts, and the number of hosts accepting the proposal matches the total number of hosts. Unfortunately, this design overlooks the scenario where a party has no hosts. Consequently, the function erroneously returns false, signaling the need for a veto period even when there are no hosts available to veto the proposal.

**Recommended Mitigation**
To address this issue, it is advisable to enhance the `_hostsAccepted()` functionality to return true even when there are no hosts. This adjustment can be implemented by modifying the function as follows:

```solidity
function _hostsAccepted(
uint8 snapshotNumHosts,
uint8 numHostsAccepted
) private pure returns (bool) {
	return snapshotNumHosts == numHostsAccepted;
}
```

With this modification, the function will correctly return true in scenarios where both `snapshotNumHosts` and `numHostsAccepted` are 0, effectively addressing the oversight.

---
## [L-02] `supportsInterface()` is missing `ERC-1271`
[PartyGovernance Line 333](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L333)

**Issue Description**
he `PartyGovernance` contract implements `EIP-165` for interface support. According to `EIP-165` guidelines, the `supportsInterface()` function should return true for each `InterfaceId` implemented by the contract. The recent update to `PartyGovernance` introduces support for `EIP-1271`, which is implemented by delegating the `isValidSignature()` function to the `ProposalExecutionEngine`. However, this newly added support for `EIP-1271` is not reflected in the `supportsInterface()` function. As a result, when the `supportsInterface()` function is called with the identifier of `EIP-1271`, it incorrectly returns false, violating the expected behavior.

```solidity
function supportsInterface(bytes4 interfaceId) public pure virtual returns (bool) {
	return
		interfaceId == type(IERC721Receiver).interfaceId ||
		interfaceId == type(ERC1155TokenReceiverBase).interfaceId ||
		// ERC4906 interface ID
		interfaceId == 0x49064906;
}
```

**Recommended Mitigation**
To rectify this issue, it is recommended to update the `supportsInterface()` function to also return true for the interface identifier of `EIP-1271`. This can be achieved by enhancing the code as shown below:

```solidity
function supportsInterface(bytes4 interfaceId) public pure virtual returns (bool) {
	return
		interfaceId == type(IERC721Receiver).interfaceId ||
		interfaceId == type(ERC1155TokenReceiverBase).interfaceId ||
		interfaceId == type(IERC1271).interfaceId ||
		// ERC4906 interface ID
		interfaceId == 0x49064906;
}
```
With this modification, the `supportsInterface()` function accurately reflects the support for `EIP-1271`, aligning with the updated functionality in the contract.

---
## [L-03] emergency execute can not be enabled again in Party & Crowdfund)
[PartyGovernance Line 855-857](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L855C14-L857)
[ETHCrowdfundBase.sol Line 376-378](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L376-L378)

**Issue Description**
The current implementation of party governance provides the DAO with the ability, in case of an emergency, to use the `emergencyExecute()` function to rescue funds. However, the existing implementation only allows the DAO or a host to permanently disable this functionality using the `disableEmergencyExecute()` function. While it makes sense for the DAO to have a one-way ability to disable this function, it would be beneficial for a host to have the ability to enable it again, especially in emergency situations where funds need to be rescued.

**Recommended Mitigation**
To address this limitation, it is recommended to enhance the contract by adding an additional function called `enableEmergencyExecute()`, which can only be called by a host. This function would enable the emergency execute functionality, providing a mechanism for hosts to reinstate it if needed. The suggested implementation is as follows:

```solidity
function enableEmergencyExecute() external {
	_assertHost(msg.sender);
	emergencyExecuteDisabled = false;
	emit EmergencyExecuteEnabled();
}
```

By incorporating this modification, hosts gain the capability to re-enable the emergency execute functionality in critical situations, allowing for a more flexible and responsive governance model.

---
## [L-04] `passThresholdBps` can not be set to 0
[SetGovernanceParameterProposal.sol Line 53-63](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol#L53-L63)

**Issue Description**
The introduced functionality allows the party to modify its governance settings through a `SetGovernanceParameterProposal`. This proposal includes the parameter `passThresholdBps`, and if the value falls within the range `0 < passThresholdBps < 10000`, it is set accordingly. However, if the value is 0, it is interpreted as no change desired, and nothing is set. The problem arises when the party intends to set this value to 0, as the current implementation does not permit this action.

**Recommended Mitigation**
To address this limitation, it is suggested to use `type(uint16).max` to indicate that no change is desired, as values above 10000 are already rejected. This adjustment would allow setting the value to 0 without conflicting with the existing implementation.

---
## [L-05] `executionDelay` can not be set to 0
[SetGovernanceParameterProposal.sol Line 42-52](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol#L42-L52)

**Issue Description**
The introduced functionality allows the party to modify its governance settings through a `SetGovernanceParameterProposal`. This proposal includes the parameter `executionDelay`, and if the value falls within the range `0 < executionDelay < 30 days`, it is set accordingly. However, if the value is 0, it is interpreted as no change desired, and nothing is set. The problem arises when the party intends to set this value to 0, as the current implementation does not permit this action.

**Recommended Mitigation**
To address this limitation, it is suggested to use `type(uint40).max` to indicate that no change is desired, as values above `30 days` are already rejected. This adjustment would allow setting the value to 0 without conflicting with the existing implementation.
