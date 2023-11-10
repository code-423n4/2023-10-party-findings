## \[G-1\] Make 3 event parameters indexed when possible

It’s the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L96

```diff
-      event Refunded(address indexed contributor, uint256 indexed tokenId, uint256 amount);
+      event Refunded(address indexed contributor, uint256 indexed tokenId, uint256 indexed amount);
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L149

```diff
-    event ProposalVetoed(uint256 indexed proposalId, address host);
+    event ProposalVetoed(uint256 indexed proposalId, address host);
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L150

```diff
-    event ProposalExecuted(uint256 indexed proposalId, address executor, bytes nextProgressData);
+    event ProposalExecuted(uint256 indexed proposalId, address executor, bytes indexed nextProgressData);
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L160C5-L167C1

```diff
-    event PartyVotingSnapshotCreated(
        address indexed voter,
        uint40 timestamp,
        uint96 delegatedVotingPower,
        uint96 intrinsicVotingPower,
        bool isDelegated
    );
```

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L32

```
event PartyCardIntrinsicVotingPowerSet(uint256 indexed tokenId, uint256 intrinsicVotingPower);
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L15C5-L19C7

```
event SigningThresholdBpsSet(
        Party indexed party,
        uint96 oldThresholdBps,
        uint96 newThresholdBps
    );
```

## \[G-2\] Use hardcode address instead address(this)

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.

References: https://book.getfoundry.sh/reference/forge-std/compute-create-address

https://twitter.com/transmissions11/status/1518507047943245824

```
(bool s, bytes memory r) = address(this).call(
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L358C40-L358C47

```
authorities[authoritiesLength - 1] = address(this);
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L382C59-L382C60

```
? address(this).balance
                    : withdrawTokens[i].balanceOf(address(this));
```

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L387C18-L388C66

```
implementation = address(this);
```

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol#L18

## \[G-3\] Use  modifier instead of function for gas efficient

It’s better to use a modifier instead of `_assertHost`  function. This is also more gas efficient as it does not control with external call.

Modifiers are a way to encapsulate common preconditions or checks and apply them to multiple functions. This can help save gas and reduce code duplication.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L216C2-L220C6

```
function _assertHost() internal view {
        if (!isHost[msg.sender]) {
            revert NotAuthorized();
        }
    }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L222C5-L228C6

```
function _assertActiveMember() internal view {
        VotingPowerSnapshot memory snap = _getLastVotingPowerSnapshotForVoter(msg.sender);
        // Must have either delegated voting power or intrinsic voting power.
        if (snap.intrinsicVotingPower == 0 && snap.delegatedVotingPower == 0) {
            revert NotAuthorized();
        }
    }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L258C5-L262C6

```
function _assertNotGloballyDisabled() internal view {
        if (_GLOBALS.getBool(LibGlobals.GLOBAL_DISABLE_PARTY_ACTIONS)) {
            revert OnlyWhenEnabledError();
        }
    }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L61C5-L65C6

```
function _assertAuthority() internal view {
        if (!isAuthority[msg.sender]) {
            revert NotAuthorized();
        }
    }
```

## \[G-3\] Public functions not called in the contract should be marked External

Declaring functions as public costs more gas. Replace them with external wherever possible.

Instances of this issue :

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L333C14-L333C31

```
function supportsInterface(bytes4 interfaceId) public pure virtual returns (bool) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L123C4-L126C6

```
function tokenURI(uint256) public view override returns (string memory) {
        _delegateToRenderer();
        return ""; // Just to make the compiler happy.
    }
```

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L129C5-L132C6

```
function contractURI() external view returns (string memory) {
        _delegateToRenderer();
        return ""; // Just to make the compiler happy.
    }
```

&nbsp;

## \[G-4\] Use of Named Returns for Local Variables Saves Gas

You can have further advantages in term of gas cost by simply using named return values as temporary local variable. As an example, the following instance of code block can refactored as follows:

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L318

```
function refund(uint256 tokenId) external returns (uint96 amount) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L383

```
) external view returns (ProposalStatus status, ProposalStateValues memory values) {
```

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L358

```
) external view returns (uint96 votingPower) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L372

```
) public view returns (uint96 votingPower) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L426

```
) public view returns (uint256 index) {
```

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L595

```
function accept(uint256 proposalId, uint256 snapIndex) public returns (uint256 totalVotes) {
```

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L868C41-L868C41

```
) private returns (bool completed) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L299

```
) private pure returns (ProposalType proposalType, bytes memory offsetProposalData) {
```

## <ins>\[G-5\] Use bytes datatype for constants instead of strings</ins>

Use a fixed size bytes datatype like bytes4 in place of string.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L123C4-L126C6

```
function tokenURI(uint256) public view override returns (string memory) {
        _delegateToRenderer();
        return ""; // Just to make the compiler happy.
    }
```

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L129C5-L132C6

```
function contractURI() external view returns (string memory) {
        _delegateToRenderer();
        return ""; // Just to make the compiler happy.
    }
```

&nbsp;

## \[G-6\] require() Should Be Used Instead Of assert()

assert() will consume all remaining gas and should only be used for conditions that are truly impossible to fail.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L499

```
assert(false); // Will not be reached.
    }
```

&nbsp;https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L168

```
assert(currentInProgressProposalId == 0);
```

## \[G-07\] same calculation twice 

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L179C8-L183C10

```
if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower_) {
            unchecked {
                votingPower_ = totalVotingPower - mintedVotingPower_;
            }
        }
```

&nbsp;`totalVotingPower - mintedVotingPower_`  should be cached 

## \[G‑8\] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.

```
function _initProposalImpl(IProposalExecutionEngine impl, bytes memory initData) internal {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L43C14-L43C31

```
937:     function _transferVotingPower(address from, address to, uint256 power) internal {
```

https://github.com/code-423n4/2023-10-party/blob/053fb9345b0739b3c26d12e1eae1eefbfd70b223/contracts/party/PartyGovernance.sol#L937

## \[G‑9\] modifier not called by the contract should be removed to save deployment gas

```
modifier onlyInitialize() {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol#L29

&nbsp;

&nbsp;