## \[L-01\] Stop Using Solidity's transfer() Now
**Proof Of Concept**
https://consensys.io/diligence/blog/2019/09/stop-using-soliditys-transfer-now/
```
  if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L224C7-L224C74

## \[L-02\] Keccak Constant values should used to immutable rather than constant
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

```
uint256 private constant _STORAGE_SLOT = uint256(keccak256("ProposalExecutionEngine.Storage"));
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol#L104C5-L104C100

```
 uint256 private constant _SET_SIGNATURE_VALIDATOR_PROPOSAL_STORAGE_SLOT =
        uint256(keccak256("SetSignatureValidatorProposal.Storage"));

```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol#L13C4-L15C1

## \[L-03\] Divide before multiply
Solidity's integer division truncates. Thus, performing division before multiplication can lead to precision loss.
```
  uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1115C7-L1115C73

```
     withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L393C16-L393C97

## \[L-04\] Function Calls in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions. Here are some of the instances entailed:
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
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L378C13-L395C14

## \[L-05\]  Empty receive()/payable fallback() function does not authenticate requests
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.
```
  fallback() external {
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L312C3-L312C26



## \[N-01\] Showing the actual values of numbers in NatSpec comments makes checking and reading code easier
```
  return (uint256(voteCount) * 1e4) / uint256(totalVotingPower) >= uint256(passThresholdBps);
    }
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1134C7-L1135C6

```
   uint256 fee = (amount * feeBps_) / 1e4;
```

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L413C14-L413C56

## \[N-02\] Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

## \[N-03\] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of
Solidity, which can make the code more insecure and more error-prone.