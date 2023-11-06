### [NC-01] Convert Functions to Modifiers (4 instances)

**Description:** Converting these functions into modifiers is recommended to enhance code organization and maintain consistency with Solidity best practices. Modifiers can improve code readability and reusability.

File: contracts/party/PartyGovernance.sol (3 instances)

```solidity

216: function _assertHost() internal view {

222: function _assertActiveMember() internal view {

256: function _assertNotGloballyDisabled() internal view {

```

File: contracts/party/PartyGovernanceNFT.sol (1 instance)

```solidity

61: function _assertAuthority() internal view {

```