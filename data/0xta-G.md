## [G-1] Batch refunds to optimize gas

The batchRefund() function currently refunds token owners individually by calling refund() on each one sequentially:

```solidity
for (uint256 i; i < numRefunds; ++i) {
  (bool s, bytes memory r) = address(this).call(
    abi.encodeCall(this.refund, (tokenIds[i]))
  );

  // ...
}
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L357

This repeats the base cost for each refund call.

It could be optimized by batching the transfers into a single call to an ERC20/ERC721's transfer()/transferFrom()function.

For example, loop through all recipients and amounts, build arrays, then call:

```solidity
party.batchTransfer(recipients, amounts);
```

This would consolidate the costs and reduce total gas usage, especially for many refunds.

## [G-2] Batch party minting on contribution

When users contribute to the crowdfund, a new party card is minted for them individually:

```solidity
function _contribute(contributor, delegate, amount) {

  //...

  if (tokenId == 0) {
    party.mint(contributor, votingPower, delegate);
  }

  //...

}
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L275

This repeats the cost of minting for each contribution.

It could be optimized by collecting the list of contributors and voting powers, then calling a batched mint function:

```solidity
contributors = [];
powers = [];

// collect in loop

party.batchMint(contributors, powers);
```

The party contract would implement a batchMint() that mints all cards in one call.

This avoids repeating the base minting cost and improves gas scaling for multiple contributions.

## [G-3] Using Immutable Storage for References

The OffChainSignatureValidator.sol contract currently performs storage lookups each time common references like addresses and selector values are used. This wastes gas by repeating the lookup cost unnecessarily.

Declare common references like the EIP-1271 signature validator selector and address types as public immutable variables initialized directly from storage.

Each storage lookup costs approx 200 gas. By declaring references immutable, their value is hardcoded at deployment saving repeated lookup costs.

### Implementation

1. Declare eip1271Validator bytes4 constant
2. Declare partyRegistry and other addresses as immutable
3. Replace usages of hardcoded values with references

For example:

```solidity
bytes4 public immutable eip1271Validator = IERC1271.isValidSignature.selector;

function isValidSignature() external view returns(bytes4) {
  return eip1271Validator;
}
```

Vs original repeated lookups.

Expected Savings 200 gas saved per usage, scaling with number of reference lookups. Significant for common references.

So Initializing immutable references from storage improves gas efficiency by avoiding repeated lookups for fixed values.

## [G-4] Separate Party Data Contract

The current `OffChainSignatureValidator.sol` contract stores party specific data like signing thresholds in a mapping within the main contract. This wastes storage gas as the data grows with number of parties.

Extract party specific data storage and logic into a separate nested contract that manages individual thresholds.

The main contract then simply delegates to these threshold managers.

### Example

```solidity
// Current approach
contract Validator {

  mapping(Party => uint) thresholds;

  //...

}

// Optimized approach
contract Validator {

  mapping(Party => ThresholdManager) public managers;

  //...

}

contract ThresholdManager {

  uint public threshold;

  //...

}

```

### Benefits

1. Storage costs distributed per party rather than all in one place
2. Threshold managers can independently manage/modify their own data
3. Main contract interface remains the same

### Estimated Savings

Storage costs are 20,000 - 30,000 gas per slot. Splitting data saves this cost per party over long term growth.

## [G-5] Caching Signer Voting Powers

The `OffChainSignatureValidator.sol` contract currently recalculates the signer's voting power from the party on each validation by querying storage.Cache previously calculated voting powers in a mapping to avoid repeated lookups. Only recalculate if signer's power has changed.

### Implementation

1. Add mapping to cache mapping(address => uint96) private cachedPowers
2. Before querying party, check if signer's power exists in cache
3. If not found or outdated, retrieve from party and save to cache
4. Otherwise directly use cached value

For-Example

```solidity
function validate() {

  uint96 power = cachedPowers[signer];

  if(power == 0) {
    power = party.getVotingPower(signer);
    cachedPowers[signer] = power;
  }

  // use cached power
}
```

Each storage read costs ~200 gas. This avoids a read per validation.

## [G-6] Use fixed point math for voting power instead of uint256 to reduce casting costs in PartyGovernanceNFT.sol contract

- **Problem**

  - Current contract uses uint256 to represent voting power
  - This requires casting between uint256 and int192 when transferring power
  - Casting has gas costs that add up for every transfer

- **Solution**

  - Represent voting power as a fixed point number using uint96
  - Store fractional part of fixed point value in last 96 bits of a uint96
  - Define total voting power as uint96 as well

- **Implementation**

  - Define GovernanceValues struct with totalVotingPower as uint96
  - Make votingPowerByTokenId a mapping from uint256 to uint96
  - Calculate voting power fractions as uint96 instead of uint256
  - Use safe casting between uint96 and int192 for power transfers
  - Update functions to increment/decrement powers as uint96

- **Benefits**

  - Avoids costly casting between uint256 and uint96/int192
  - Fractional calculations can be done entirely in fixed point
  - Reduces overall gas costs for operations involving voting powers

- **Estimates**

  - Casting between uint types costs ~20 gas
  - Every transfer and calculation was casting
  - Fixed point reduces casts to just transfer functions
  - Potential savings of 20 gas x number of non-transfer ops