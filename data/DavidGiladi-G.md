
### Gas Optimization Issues
|Title|Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
|[G-1] Optimal Struct Variable Order | [Optimal Struct Variable Order](#optimal-struct-variable-order) | 2 | 10000 |
|[G-2] Inefficient use of abi.encode() | [Inefficient use of abi.encode()](#inefficient-use-of-abiencode) | 1 | 100 |
|[G-3] Multiplication and Division by 2 Should use in Bit Shifting | [Multiplication and Division by 2 Should use in Bit Shifting](#multiplication-and-division-by-2-should-use-in-bit-shifting) | 2 | 40 |
|[G-4] Check Arguments Early | [Check Arguments Early](#check-arguments-early) | 9 | - |
|[G-11] Cache External Calls in Loops | [Cache External Calls in Loops](#cache-external-calls-in-loops) | 5 | 500 |
|[G-5] Optimal State Variable Order | [Optimal State Variable Order](#optimal-state-variable-order) | 1 | 5000 |
|[G-6] Redundant state variable getters | [Redundant state variable getters](#redundant-state-variable-getters) | 1 | - |
|[G-7] Superfluous event fields | [Superfluous event fields](#superfluous-event-fields) | 1 | - |
|[G-8] Avoid Unnecessary Computation Inside Loops | [Avoid Unnecessary Computation Inside Loops](#avoid-unnecessary-computation-inside-loops) | 2 | - |
|[G-9] Redundant Contract Existence Check in Consecutive External Calls | [Redundant Contract Existence Check in Consecutive External Calls](#redundant-contract-existence-check-in-consecutive-external-calls) | 1 | 100 |
|[G-10] Unused state variable | [Unused state variable](#unused-state-variable) | 1 | 2900 |
|[G-11] Use Assembly for Hash Calculation | [Use Assembly for Hash Calculation](#use-assembly-for-hash-calculation) | 7 | 560 |

Total: 11 issues

#


## Optimal Struct Variable Order
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 10000

### Description
Detect optimal variable order in struct definitions to decrease the number of slots used. Each storage slot used can cost at least 5,000 gas for each write operation, and potentially up to 20,000 gas if you're turning a zero value into a non-zero value. Hence, optimizing storage usage can result in significant gas savings. The real-world savings could vary depending on your contract's specific logic and the state of the Ethereum network.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#
Optimization opportunity in struct 
```
File: contracts/crowdfund/ETHCrowdfundBase.sol

33    struct ETHCrowdfundOptions {
34            Party party;
35            address payable initialContributor;
36            address initialDelegate;
37            uint96 minContribution;
38            uint96 maxContribution;
39            bool disableContributingForExistingCard;
40            uint96 minTotalContributions;
41            uint96 maxTotalContributions;
42            uint16 exchangeRateBps;
43            uint16 fundingSplitBps;
44            address payable fundingSplitRecipient;
45            uint40 duration;
46            IGateKeeper gateKeeper;
47            bytes12 gateKeeperId;
48        }
```

- original variable order (count: 7 slots)
    - Party party
    - address initialContributor
    - address initialDelegate
    - uint96 minContribution
    - uint96 maxContribution
    - bool disableContributingForExistingCard
    - uint96 minTotalContributions
    - uint96 maxTotalContributions
    - uint16 exchangeRateBps
    - uint16 fundingSplitBps
    - address fundingSplitRecipient
    - uint40 duration
    - IGateKeeper gateKeeper
    - bytes12 gateKeeperId


 - optimized variable order (count: 6 slots)
    - Party party
    - uint96 minContribution
    - address initialContributor
    - uint96 maxContribution
    - address initialDelegate
    - uint96 minTotalContributions
    - address fundingSplitRecipient
    - uint96 maxTotalContributions
    - IGateKeeper gateKeeper
    - bytes12 gateKeeperId
    - uint40 duration
    - uint16 exchangeRateBps
    - uint16 fundingSplitBps
    - bool disableContributingForExistingCard


[https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L33-L48](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L33-L48)


#
Optimization opportunity in struct 
```
File: contracts/crowdfund/InitialETHCrowdfund.sol

26    struct InitialETHCrowdfundOptions {
27            address payable initialContributor;
28            address initialDelegate;
29            uint96 minContribution;
30            uint96 maxContribution;
31            bool disableContributingForExistingCard;
32            uint96 minTotalContributions;
33            uint96 maxTotalContributions;
34            uint16 exchangeRateBps;
35            uint16 fundingSplitBps;
36            address payable fundingSplitRecipient;
37            uint40 duration;
38            IGateKeeper gateKeeper;
39            bytes12 gateKeeperId;
40        }
```

- original variable order (count: 6 slots)
    - address initialContributor
    - address initialDelegate
    - uint96 minContribution
    - uint96 maxContribution
    - bool disableContributingForExistingCard
    - uint96 minTotalContributions
    - uint96 maxTotalContributions
    - uint16 exchangeRateBps
    - uint16 fundingSplitBps
    - address fundingSplitRecipient
    - uint40 duration
    - IGateKeeper gateKeeper
    - bytes12 gateKeeperId


 - optimized variable order (count: 5 slots)
    - address initialContributor
    - uint96 minContribution
    - address initialDelegate
    - uint96 maxContribution
    - address fundingSplitRecipient
    - uint96 minTotalContributions
    - IGateKeeper gateKeeper
    - uint96 maxTotalContributions
    - bytes12 gateKeeperId
    - uint40 duration
    - uint16 exchangeRateBps
    - uint16 fundingSplitBps
    - bool disableContributingForExistingCard


[https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L26-L40](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L26-L40)


</details>

# 


## Inefficient use of abi.encode()
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 100

### Description
The `abi.encode()` function is less gas efficient than `abi.encodePacked()`, especially when encoding only static types. This detector identifies instances where `abi.encode()` is used and all arguments are static, suggesting that `abi.encodePacked()` could potentially be used instead for better gas efficiency. Note: `abi.encodePacked()` should not be used if any of the arguments are dynamic types, as it could lead to hash collisions.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
#

```
File: contracts/party/PartyGovernance.sol

284    _initProposalImpl(
285                IProposalExecutionEngine(_GLOBALS.getAddress(LibGlobals.GLOBAL_PROPOSAL_ENGINE_IMPL)),
286                abi.encode(proposalEngineOpts)
287            )
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L284-L287](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L284-L287)


</details>

# 



## Multiplication and Division by 2 Should use in Bit Shifting
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 40

### Note 

reported only on 2 issues that was missing in the winning bot. 

### Description
The expressions 'x * 2' and 'x / 2' can be optimized for gas efficiency by utilizing bitwise operations. In Solidity, you can achieve the same results by using bitwise left shift (x << 1) for multiplication and bitwise right shift (x >> 1) for division.

Using bitwise shift operations (SHL and SHR) instead of multiplication (MUL) and division (DIV) opcodes can lead to significant gas savings. The MUL and DIV opcodes cost 5 gas, while the SHL and SHR opcodes incur a lower cost of only 3 gas.

By leveraging these more efficient bitwise operations, you can reduce the gas consumption of your smart contracts and enhance their overall performance.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#




```
File: contracts/party/PartyGovernance.sol

1159    mstore(0x00, keccak256(add(preciousTokens, 0x20), mul(mload(preciousTokens), 0x20)))
```
 instead `32` use bit shifting `5` 

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1159](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1159)


#

```
File: contracts/party/PartyGovernance.sol

1160    mstore(0x20, keccak256(add(preciousTokenIds, 0x20), mul(mload(preciousTokenIds), 0x20)))
```
 instead `32` use bit shifting `5` 

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1160](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1160)


</details>

# 


## Cache External Calls in Loops
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 500

### Description
This detector identifies instances of repeated external calls within loops. By moving the first external call outside of the loop and using low-level calls inside the loop, it is possible to save gas by avoiding repeated EXTCODESIZE opcodes, as there is no need to check again if the contract exists. 

Note: This detector only triggers if the function call does not return any value.

Prior to 0.8.10, the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent Solidity versions the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low-level calls never check for contract existence. 

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
#

```
File: contracts/crowdfund/InitialETHCrowdfund.sol

364    r.rawRevert()
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L364](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L364)


#

```
File: contracts/party/PartyGovernanceNFT.sol

420    payable(feeRecipient).transferEth(fee)
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L420](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L420)


#

```
File: contracts/party/PartyGovernanceNFT.sol

436    payable(receiver).transferEth(amount)
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L436](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L436)


#

```
File: contracts/party/PartyGovernanceNFT.sol

438    token.compatTransfer(receiver, amount)
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L438](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L438)


#

```
File: contracts/party/PartyGovernanceNFT.sol

422    token.compatTransfer(feeRecipient, fee)
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L422](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L422)


</details>

# 


## Optimal State Variable Order
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 5000

### Note 
The wining bot does not consider all the variables and only orders some of them, resulting in an order of 8 instead of the expected 9.


### Description
Detects optimal variable order in contract storage layouts to decrease the number of storage slots used. Each storage slot used can cost at least 5,000 gas for each write operation, and potentially up to 20,000 gas if you're turning a zero value into a non-zero value. Hence, optimizing storage usage can result in significant gas savings. The real-world savings could vary depending on your contract's specific logic and the state of the Ethereum network.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
#
Optimization opportunity in storage variable layout of contract 
```
File: contracts/party/PartyGovernance.sol

23    abstract contract PartyGovernance is
24        ProposalStorage,
25        Implementation,
26        IERC4906,
27        ReadOnlyDelegateCall
28    
```

- original variable order (count: 10 slots)
    - uint256 UINT40_HIGH_BIT
    - uint96 VETO_VALUE
    - IGlobals _GLOBALS
    - bool emergencyExecuteDisabled
    - uint16 feeBps
    - address feeRecipient
    - uint40 lastRageQuitTimestamp
    - bytes32 preciousListHash
    - uint256 lastProposalId
    - mapping(address => bool) isHost
    - mapping(address => address) delegationsByVoter
    - uint8 numHosts
    - mapping(uint256 => PartyGovernance.ProposalState) _proposalStateByProposalId
    - mapping(address => PartyGovernance.VotingPowerSnapshot[]) _votingPowerSnapshotsByVoter


 - optimized variable order (count: 9 slots)
    - IGlobals _GLOBALS
    - uint96 VETO_VALUE
    - address feeRecipient
    - uint40 lastRageQuitTimestamp
    - uint16 feeBps
    - bool emergencyExecuteDisabled
    - uint8 numHosts
    - uint256 UINT40_HIGH_BIT
    - bytes32 preciousListHash
    - uint256 lastProposalId
    - mapping(address => bool) isHost
    - mapping(address => address) delegationsByVoter
    - mapping(uint256 => PartyGovernance.ProposalState) _proposalStateByProposalId
    - mapping(address => PartyGovernance.VotingPowerSnapshot[]) _votingPowerSnapshotsByVoter



Recomended optimizations will use 1 less slots.

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L23-L1172](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L23-L1172)


</details>

# 



## Redundant state variable getters
- Severity: Gas Optimization
- Confidence: High

### Description
Detects and reports state variables that have manually coded getters. Getters for public state variables are automatically generated in Solidity. Coding them manually is redundant and a waste of gas.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
#

```
File: contracts/party/PartyGovernanceNFT.sol

146    function getDistributionShareOf(uint256 tokenId) external view returns (uint256) 
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L146-L148](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L146-L148)


</details>

# 




## Superfluous event fields
- Severity: Gas Optimization
- Confidence: High

### Description
Block.timestamp and block.number are added to event information by default so adding them manually wastes gas.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
#

```
File: contracts/party/PartyGovernance.sol

160    event PartyVotingSnapshotCreated(
161            address indexed voter,
162            uint40 timestamp,
163            uint96 delegatedVotingPower,
164            uint96 intrinsicVotingPower,
165            bool isDelegated
166        );
```
The event `PartyVotingSnapshotCreated` in the contract `PartyGovernance` includes the field `timestamp` which is superfluous:
[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L160-L166](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L160-L166)


</details>

# 



## Avoid Unnecessary Computation Inside Loops
- Severity: Gas Optimization
- Confidence: High

### Description
This detector identifies instances of computations being performed inside loops that could be performed outside the loop to save gas.

Unnecessary computation inside loops:

Any computation performed inside a loop that doesn't change with each iteration can be moved outside the loop to save gas.This detector identifies instances where the following unnecessary computations are performed inside loops:

- 1 Local variables are read inside loops but never modified within the same loop, indicating they could be cached outside the loop.

- 2 Computations involving constant expressions (literals) which result in the same output in every loop iteration.

By addressing these issues, gas usage can be optimized.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: contracts/party/PartyGovernanceNFT.sol

295    _adjustVotingPower(owner, -votingPower.safeCastUint96ToInt192(), address(0))
```
The following computations can be cached outside of loops for gas optimization `- votingPower.safeCastUint96ToInt192()`<br>.
[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L295](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L295)


#

```
File: contracts/crowdfund/InitialETHCrowdfund.sol

379    i < authoritiesLength - 1
```
The following computations can be cached outside of loops for gas optimization `authoritiesLength - 1`<br>.
[https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L379)


</details>

# 


## Redundant Contract Existence Check in Consecutive External Calls
- Severity: Gas Optimization
- Confidence: Medium
- Total Gas Saved: 100

### Description
This detector identifies instances where there are consecutive external calls to the same contract, where the subsequent calls could use a low-level call to save gas.

Note: This detector only triggers if the function call does not return any value. 

Prior to 0.8.10, the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent Solidity versions the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low-level calls never check for contract existence. 

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
#

```
File: contracts/crowdfund/InitialETHCrowdfund.sol

336    party.burn(tokenId)
```
This call could be replaced with a low-level call because the contract `Party` has already been checked in <br>`Line: 328    uint96 votingPower = party.votingPowerByTokenId(tokenId).safeCastUint256ToUint96()`<br>
[https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L336](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L336)


</details>

# 



## Unused state variable
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 2900

### Description
Unused state variable.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
#

```
File: contracts/proposals/ProposalStorage.sol

39    uint256 internal constant PROPOSAL_FLAG_UNANIMOUS = 0x1
```
 is never used in 
```
File: contracts/proposals/ProposalExecutionEngine.sol

24    contract ProposalExecutionEngine is
25        IProposalExecutionEngine,
26        Implementation,
27        ProposalStorage,
28        ListOnOpenseaProposal,
29        ListOnOpenseaAdvancedProposal,
30        ListOnZoraProposal,
31        FractionalizeProposal,
32        ArbitraryCallsProposal,
33        DistributeProposal,
34        AddAuthorityProposal,
35        OperatorProposal,
36        SetSignatureValidatorProposal,
37        SetGovernanceParameterProposal,
38        IERC1271
39    
```


[https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L39](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L39)


</details>

# 



## Use Assembly for Hash Calculation
- Severity: Gas Optimization
- Confidence: Medium
- Total Gas Saved: 560

### Description
Detects places in the code where hash calculation could be done using inline assembly to optimize gas usage.

<details>

<summary>
There are 7 instances of this issue:

</summary>

###
#

```
File: contracts/party/PartyGovernance.sol

406    bytes32 dataHash = keccak256(proposal.proposalData)
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L406](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L406)


#

```
File: contracts/proposals/ProposalExecutionEngine.sol

170    revert ProposalProgressDataInvalidError(
171                            keccak256(params.progressData),
172                            nextProgressDataHash
173                        )
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L170-L173](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L170-L173)


#

```
File: contracts/proposals/ProposalExecutionEngine.sol

177    bytes32 progressDataHash = keccak256(params.progressData)
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L177](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L177)


#

```
File: contracts/proposals/ProposalExecutionEngine.sol

202    stor.nextProgressDataHash = keccak256(nextProgressData)
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L202](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L202)


#

```
File: contracts/proposals/ProposalStorage.sol

40    uint256 private constant SHARED_STORAGE_SLOT =
41            uint256(keccak256("ProposalStorage.SharedProposalStorage"))
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L40-L41](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol#L40-L41)


#

```
File: contracts/proposals/ProposalExecutionEngine.sol

104    uint256 private constant _STORAGE_SLOT = uint256(keccak256("ProposalExecutionEngine.Storage"))
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L104](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L104)


#

```
File: contracts/proposals/SetSignatureValidatorProposal.sol

13    uint256 private constant _SET_SIGNATURE_VALIDATOR_PROPOSAL_STORAGE_SLOT =
14            uint256(keccak256("SetSignatureValidatorProposal.Storage"))
```

[https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L13-L14](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L13-L14)


</details>

# 
