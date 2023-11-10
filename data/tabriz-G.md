
## [G-01] Expressions for constant values such as a call to keccak256() ,should use immutable rather than constant
```
uint256 private constant _STORAGE_SLOT = uint256(keccak256("ProposalExecutionEngine.Storage"));
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L104C5-L104C100

```
 uint256 private constant SHARED_STORAGE_SLOT =
        uint256(keccak256("ProposalStorage.SharedProposalStorage"));
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L40C4-L41C69

```
 uint256 private constant _SET_SIGNATURE_VALIDATOR_PROPOSAL_STORAGE_SLOT =
        uint256(keccak256("SetSignatureValidatorProposal.Storage"));
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L13C4-L14C69
## [G-02] Make for loop unchecked
The risk of for loops getting overflowed is extremely low. Because it always increments by 1 and is
limited to the arrays length. Even if the arrays are extremely long, it will take a massive amount of time
and gas to let the for loop overflow.

```
 for (uint256 i; i < numContributions; ++i) {
            ethAvailable -= args.values[i];

            votingPowers[i] = _contribute(
                payable(msg.sender),
                args.delegate,
                args.values[i],
                args.tokenIds[i],
                args.gateDatas[i]
            );
        }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L211C8-L221C10

```
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
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L260C8-L269C10

```
 for (uint256 i; i < numRefunds; ++i) {
            (bool s, bytes memory r) = address(this).call(
                abi.encodeCall(this.refund, (tokenIds[i]))
            );

```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L357C8-L361C1

```
 for (uint i = 0; i < authoritiesLength - 1; ++i) {
            authorities[i] = opts.authorities[i];
        }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379C8-L381C10

```
 for (uint256 i; i < withdrawTokens.length; ++i) {
                // Check if order of tokens to transfer is valid.
                // Prevent null and duplicate transfers.
                if (prevToken >= withdrawTokens[i]) revert InvalidTokenOrderError();

                prevToken = withdrawTokens[i];

                // Check token's balance.
                uint256 balance = address(withdrawTokens[i]) == ETH_ADDRESS
                    ? address(this).balance
                    : withdrawTokens[i].balanceOf(address(this));

                // Add fair share of tokens from the party to total.
                for (uint256 j; j < tokenIds.length; ++j) {
                    // Must be retrieved before burning the token.
                    withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
                }
            }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L378C12-L395C14

## [G-03] preciousTokenIds (Save 1 SLOT: 2k gas)
preciousTokenIds is timestamp thus changing it to uint64 should be safe for around 532
years.

```
 uint256[] preciousTokenIds;
        // The timestamp until which ragequit is enabled.
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L58C8-L59C58

## [G-04] Multiple accesses of a mapping/array should use a storage pointer
Caching a mapping’s value in a storage pointer when the value is accessed multiple times saves ~40
gas per access due to not having to perform the same offset calculation every time. Help the Optimizer
by saving a storage variable’s reference instead of repeatedly fetching it.
To achieve this, declare a storage pointer for the variable and use it instead of repeatedly fetching the
reference in a map or an array.

```
  mapping(address => VotingPowerSnapshot[]) private _votingPowerSnapshotsByVoter;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L214C3-L214C84



## [G-05] Use hardcode address instead address(this)
Instead of using address(this) , it is more gas-efficient to pre-calculate and use the hardcoded
address . Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.
References: https://book.getfoundry.sh/reference/forge-std/compute-create-address
https://twitter.com/transmissions11/status/1518507047943245824
```
 (bool s, bytes memory r) = address(this).call(
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L358C12-L358C59

```
  authorities[authoritiesLength - 1] = address(this);

```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L382C7-L383C1


```
 if (msg.sender != address(this)) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L496C8-L496C43

```
   Party(payable(address(this))),
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L538C14-L538C47

```
       if (msg.sender != address(this)) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L68C2-L68C43

```
 ? address(this).balance
                    : withdrawTokens[i].balanceOf(address(this));
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L387C20-L388C66

```

    constructor() {
        implementation = address(this);
    }

    // Reverts if the current function context is not inside of a delegatecall.
    modifier onlyDelegateCall() virtual {
        if (address(this) == implementation) {
            revert OnlyDelegateCallError();
        }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol#L16C1-L25C10

## [G-06] `variable == false` instead of `!variable`.
a bit cheapier when you replace:

```
if (!_gateKeeper.isAllowed(msg.sender, gateKeeperId, gateData)) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L290C13-L290C78

```
 if (!s) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L362C12-L362C22

```
    if (!completed) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L756C5-L756C26

```
        if (!success) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L826C5-L826C28

```
  if (!success) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L847C7-L847C24

```
   if (!success) {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L889C10-L889C28

```
      if (!s) {
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L50C3-L50C18