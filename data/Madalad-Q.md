# Low Severity

## [L-01] Take steps to prevent against metamorphic contract attack

In May 2023, Tornado Cash experienced an exploit to their governance contract during which the attacker made use of `CREATE`, `CREATE2` and `SELFDESTRUCT` to drain a large amount of funds. All governance contracts, including `Party`, are susceptible to such an attack. Strongly consider using off-chain monitoring to detect proposals that call suspicious contracts using the above opcodes, utilising the `veto` function as required.

References:
- [Tornado Cash governance hack - Medium](https://medium.com/coinmonks/tornado-cash-governance-hack-ec77ebb3aa68)
- [tornado-cash-exploit PoC from pcaversaccio](https://github.com/pcaversaccio/tornado-cash-exploit/tree/main).

## [L-02] Harcoded chain identifier may lead to cross chain replay attacks

In `OffChainSignatureValidator#isValidSignature`, the chain identifier (used to prevent cross chain replay attacks) is hardcoded as a string. This means that it will remain the same, even if the contract is deployed to a different chain or the initial chain undergoes a hard fork.

Replace the hardcoded value with a value involving `block.chainid` to fully protect against cross chain replay attacks.

```solidity
File: contracts\signature-validators\OffChainSignatureValidator.sol

48:         bytes memory encodedPacket = abi.encodePacked(
49:             "\x19Ethereum Signed Message:\n", // @audit should not be hardcoded
50:             Strings.toString(message.length),
51:             message
52:         );
53:         if (keccak256(encodedPacket) != hash) {
54:             revert MessageHashMismatch();
55:         }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L49

## [L-03] It should not be possible to decrease `totalVotingPower` below `mintedVotingPower`

In the `PartyGovernanceNFT` contract, `increaseVotingPower` prevents the caller from increasing the voting power for a `tokenId` if it would lead to `mintedVotingPower` exceeding `totalVotingPower`. Similar protection is missing from the `decreaseVotingPower` function, which could lead to unexpected behaviour, such as miscalculations in `_areVotesPassing` causing proposals to pass despite having less support than ought to be required.

Add the following check to make `decreaseTotalVotingPower` revert if it is decreased below `mintedVotingPower`.

```diff
File: contracts\party\PartyGovernanceNFT.sol

    function decreaseTotalVotingPower(uint96 votingPower) external {
        _assertAuthority();
+       require(_getSharedProposalStorage().governanceValues.totalVotingPower - votingPower >= mintedVotingPower, "minted > total");
        _getSharedProposalStorage().governanceValues.totalVotingPower -= votingPower;
    }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L255-L258

## [L-04] Minting a `PartyGovernanceNFT` that has zero `votingPower` should be prevented

This can occur in two ways; if the `votingPower` argument is passed as 0 to the `mint` function, or if `mint` is called while `mintedVotingPower` is equal to `totalVotingPower`. Party tokens are used only to vote on proposals and receive funds through `distribution` or `rageQuit`, both of which depend on a non-zero voting power. Therefore, a token with zero voting power serves no purpose and this possibility should be explicitly prevented.

Note: I submitted a medium finding titled "Authority can DoS voting by abusing `rageQuit`" in which the minting of NFTs with zero voting power enables a DoS attack. Making this change provides protection against that and any other yet to be known issues in this version or future versions of the protocol. 

```diff
File: contracts\party\PartyGovernanceNFT.sol

    function mint(
        address owner,
        uint256 votingPower,
        address delegate
    ) external returns (uint256 tokenId) {
        _assertAuthority();
        uint96 mintedVotingPower_ = mintedVotingPower;
        uint96 totalVotingPower = _getSharedProposalStorage().governanceValues.totalVotingPower;

        // Cap voting power to remaining unminted voting power supply.
        uint96 votingPower_ = votingPower.safeCastUint256ToUint96();
        // Allow minting past total voting power if minting party cards for
        // initial crowdfund when there is no total voting power.
        if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower_) {
            unchecked {
                votingPower_ = totalVotingPower - mintedVotingPower_;
            }
        }

+       if (votingPower_ == 0) revert("votingPower=0");

        // Update state.
        unchecked {
            tokenId = ++tokenCount;
        }
        mintedVotingPower += votingPower_;
        votingPowerByTokenId[tokenId] = votingPower_;

        emit PartyCardIntrinsicVotingPowerSet(tokenId, votingPower_);

        // Use delegate from party over the one set during crowdfund.
        address delegate_ = delegationsByVoter[owner];
        if (delegate_ != address(0)) {
            delegate = delegate_;
        }

        _adjustVotingPower(owner, votingPower_.safeCastUint96ToInt192(), delegate);
        _safeMint(owner, tokenId);
    }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L166-L202

## [L-05] Contracts deployed on Base mainnet may be non-functional due to incompatible Solidity version

The compiler for Solidity 0.8.20 switches the default target EVM version to [Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note), which includes the new PUSH0 op code. This op code is not supported on Base, which is built upon Optimism Bedrock ([see here](https://community.optimism.io/docs/developers/build/differences/)). See also this relevant [issue](https://github.com/ethereum/solidity/issues/14254) on the official Solidity github for reference. This means that deployment to Base will fail with error "invalid opcode: PUSH0", and the protocol will not be able to deploy on Base, contrary to the expectations set out in the README.

To mitigate this issue, use an earlier Solidity version that is compatible with Base, such as 0.8.19.

## [L-06] `PartyGovernanceNFT#burn` can be called after the party has started

NatSpec for both `burn` functions in `PartyGovernanceNFT` state that they "can only be called by an authority before the party has started". Authority of the caller is indeed checked by calling `_assertAuthority`, however no check exists anywhere in the execution path for whether or not the party has started/been initialized. If this behaviour is intended, then update the NatSpec accordingly. Otherwise, make the functions revert if the party has started (`totalVotingPower != 0`).

```solidity
File: contracts\party\PartyGovernanceNFT.sol

260:     /// @notice Burn governance NFTs and remove their voting power. Can only
261:     ///         be called by an authority before the party has started.
262:     /// @param tokenIds The IDs of the governance NFTs to burn.
263:     function burn(uint256[] memory tokenIds) public {

307:     /// @notice Burn governance NFT and remove its voting power. Can only be
308:     ///         called by an authority before the party has started.
309:     /// @param tokenId The ID of the governance NFTs to burn.
310:     function burn(uint256 tokenId) external {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L263
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L310

## [L-07] `InitialETHCrowdfund` deployer is not credited for pre-existing ETH

In `InitialETHCrowdfund#initialize`, comments explain that if there is any ETH passed with the call, or any pre-existing ETH at the address, it should be credited to the deployer. However, in the case where `msg.value` is 0 but there is pre-existing ETH at the contract address, the deployer is not credited. This is because the `if` statement checks `msg.value` instead of `address(this).balance`.

Make the following changes to achieve the desired behaviour:
```diff
        // If the deployer passed in some ETH during deployment, credit them
        // for the initial contribution.
        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
-       if (initialContribution > 0) {
+       if (address(this).balance > 0) {
            // If this contract has ETH, either passed in during deployment or
            // pre-existing, credit it to the `initialContributor`.
            _contribute(
                crowdfundOpts.initialContributor,
                crowdfundOpts.initialDelegate,
-               initialContribution,
+               address(this).balance,
                0,
                ""
            );
        }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L141

## [L-08] Authorities should be able to be removed in `PartyGovernanceNFT`

In `PartyGovernanceNFT`, authorities can be added after initialization through `addAuthority`, however there is no mechanism to revoke authority from a user unless that user does so willingly. In the event of a compromised/malicious authority, all of the funds in the party would be at an immediate risk, as authorities may call `rageQuit` to burn the tokens of other users and receive their share of the funds in the contract.

Consider implementing a `removeAuthority` function, ensuring it has the `onlySelf` modifier so that only the party itself can remove authorities. For further protection, add a call to `_assertNotGloballyDisabled` within `rageQuit` and `burn` so that the system can be paused if an authority attempts to drain the funds of the contract, and the dev team and governance can react before funds are stolen.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol

## [L-09] ETH may become trapped after calling `emergencyExecute`

`emergencyExecute` functions in both `PartyGovernance` and `ETHCrowdfundBase` contracts do not perform any checks on the amount of ether that is forwarded with the call. This means that `msg.value` may exceed the `amountEth` argument, leaving the remainder trapped in the contract.

Consider adding a check on `msg.value` somewhere in the function.

```diff
File: contracts/party/PartyGovernance.sol

    function emergencyExecute(
        address targetAddress,
        bytes calldata targetCallData,
        uint256 amountEth
    ) external payable onlyPartyDao onlyWhenEmergencyExecuteAllowed onlyDelegateCall {
+       require(msg.value == amountEth, "invalid amountEth value");
        (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
        if (!success) {
            res.rawRevert();
        }
        emit EmergencyExecute(targetAddress, targetCallData, amountEth);
    }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L841

# Non-critical Severity

## [N-01] `ETHCrowfundBase#_initialize` does not set up the gatekeeper, despite comment claiming it does

The comment above the functions claims that the gatekeeper is setup, despite this not being the case. The parameters `opts.gateKeeper` and `opts.gateKeeperId` are present but unused. This can cause confusion for users and potentially lead to mistakes from future developers. Alter the comment or add to the function to resolve the discrepency.

```solidity
File: contracts\crowdfund\ETHCrowdfundBase.sol

138:     // Initialize storage for proxy contracts, credit initial contribution (if
139:     // any), and setup gatekeeper.
140:     function _initialize(ETHCrowdfundOptions memory opts) internal {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L138-L140

## [N-02] Typo in mapping name

In `OffChainSignatureValidator`, a mapping is named `signingThersholdBps` which is a typo of `signingThresholdBps`. The automated report from the bot race detected some typos  but missed this instance.

```solidity
File: contracts\signature-validators\OffChainSignatureValidator.sol

22:     mapping(Party party => uint96 thresholdBps) public signingThersholdBps;

68:         uint96 thresholdBps = signingThersholdBps[party];

86:         emit SigningThresholdBpsSet(party, signingThersholdBps[party], thresholdBps);
87:         signingThersholdBps[party] = thresholdBps;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L22