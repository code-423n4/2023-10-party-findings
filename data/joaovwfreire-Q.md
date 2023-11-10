## qa-01 Code comment states vetoed value is -1, however it is type(uint96).max
When vetoing a proposal, it's total votes are set to type(uint96).max
```solidity
uint96 private constant VETO_VALUE = type(uint96).max;
```

But the code's comment state votes are set to -1.
```solidity
 uint96 votes; // -1 == vetoed
```
And references it twice more:
```solidity
// Setting `votes` to -1 indicates a veto.
```

```solidity
        // -1 indicates veto.
```
References:
Code snippet 1: [2023-10-party/contracts/party/PartyGovernance.sol at b23c65d62a20921c709582b0b76b387f2bb9ebb5 · code-423n4/2023-10-party (github.com)](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L187)
Code snippet 2: [2023-10-party/contracts/party/PartyGovernance.sol at b23c65d62a20921c709582b0b76b387f2bb9ebb5 · code-423n4/2023-10-party (github.com)](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L125)
Code snippet 3: [2023-10-party/contracts/party/PartyGovernance.sol at b23c65d62a20921c709582b0b76b387f2bb9ebb5 · code-423n4/2023-10-party (github.com)](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L667)
Code snippet 4: [2023-10-party/contracts/party/PartyGovernance.sol at b23c65d62a20921c709582b0b76b387f2bb9ebb5 · code-423n4/2023-10-party (github.com)](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L683C1-L683C30)

## lo-01 ExchangeRateBps rounds dust amounts to zero
ExchangeRateBps is an ETHCrowdfundBase contract global uint16 that has bounds from 1 to 10000.
When calculating newVotingPower, totalContributions_ is multiplied by exchangeRateBps then divided by 1e4. 
```solidity
uint96 newVotingPower = (totalContributions_ * exchangeRateBps) / 1e4;
```

If the exchangeRateBps is equal to 1, newVotingPower will round to zero if totalContributions_ is not at least 10000.
References: 
Code snippet 1: [2023-10-party/contracts/crowdfund/ETHCrowdfundBase.sol at main · code-423n4/2023-10-party (github.com)](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L329)

## lo-02 abdicateHost does not enforce a minimum of 1 host
When calling abdicateHost at the partyGovernance contract, the last host is not stopped from leaving as well. 
Code snippet:
```solidity
function abdicateHost(address newPartyHost) external {
        _assertHost();
        // 0 is a special case burn address.
        if (newPartyHost != address(0)) {
            // Cannot transfer host status to an existing host.
            if (isHost[newPartyHost]) {
                revert InvalidNewHostError();
            }
            isHost[newPartyHost] = true;
        } else {
            // Burned the host status
            --numHosts;
        }
        isHost[msg.sender] = false;
        emit HostStatusTransferred(msg.sender, newPartyHost);
    }
```
This makes a party vulnerable to becoming hostless at any point in time.

Reference:
Code snippet: [2023-10-party/contracts/party/PartyGovernance.sol at b23c65d62a20921c709582b0b76b387f2bb9ebb5 · code-423n4/2023-10-party (github.com)](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L457-L472)
## lo-03 setSigningThresholdBps does not enforce the 10000 upper bound
When setting a signing threshold at OffChainSignatureValidator contract, there are no sanity checks to ensure the new signing threshold bps is within reallistic bounds for the system.
```solidity
function setSigningThresholdBps(uint96 thresholdBps) external {
        Party party = Party(payable(msg.sender));
        emit SigningThresholdBpsSet(party, signingThersholdBps[party], thresholdBps);
        signingThersholdBps[party] = thresholdBps;
    }
```
Notice how there are no checks to ensure a threshold is not bigger than 100% ( or 10000 at this system).

Reference:
Code snippet: [2023-10-party/contracts/signature-validators/OffChainSignatureValidator.sol at b23c65d62a20921c709582b0b76b387f2bb9ebb5 · code-423n4/2023-10-party (github.com)](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/signature-validators/OffChainSignatureValidator.sol#L84)

## lo-04 No upper bound for voteDuration on _executeSetGovernanceParameter calls

When calling _executeSetGovernanceParameter, a party sets it's voteDuration to a value that is passed at the function call. 
There are no sanity checks to ensure the new voteDuration set is within bounds. 
```solidity
if (proposalData.voteDuration != 0) {
            if (proposalData.voteDuration < 1 hours) {
                revert InvalidGovernanceParameter(proposalData.voteDuration);
            }
            emit VoteDurationSet(
                _getSharedProposalStorage().governanceValues.voteDuration,
                proposalData.voteDuration
            );
            _getSharedProposalStorage().governanceValues.voteDuration = proposalData.voteDuration;
        }
```

This enables a vote duration to last high times.
Reference:
Code snippet:
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/proposals/SetGovernanceParameterProposal.sol#L32-L41
## lo-05 authorities are able to rageQuit regardless of a ragequit being disabled permanently
When an authority calls rageQuit, it is able to bypass key system features by avoiding the following if statement:
``` solidity
if (!isAuthority_) {
            if (currentRageQuitTimestamp != ENABLE_RAGEQUIT_PERMANENTLY) {
                if (
                    currentRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY ||
                    currentRageQuitTimestamp < block.timestamp
                ) {
                    revert CannotRageQuitError(currentRageQuitTimestamp);
                }
            }
        }
```
This enables an authority to essentially act in ways that may harm a user. It is considered low as an authority is very unlikely to exploit this, but is a vulnerability as it can bypass system security features.
Reference:
Code snippet: https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L357-L366
## lo-06 No bounds for opts.duration on ETHCrowdfundBase contract initialization leads to impossible refunds on crowdfunds
## Impact
When initializing an ETHCrowdfund, a host can set the crowdfund duration to manage it's stages. 
The first issue is the lack of sanity checks of duration, so it could be initialized with values that would take a really long time to finish. [1]
The second issue is refunds can only be called when the crowdfund was lost.[2]
And the third issue is the status only gets set to lost after the expiry timestamp is past. [3]
This means a host can create crowdfunds that last a really long time and that users cannot get their investments out of. 
## Proof of concept
[Code snippet 1](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L163)
```solidity
expiry = uint40(block.timestamp + opts.duration);
```
[Code snippet 2](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L322-L324)
```solidity
if (lc != CrowdfundLifecycle.Lost) {
                revert WrongLifecycleError(lc);
            }
```

[Code snippet 3](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L175-L194)
```solidity
function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {
        if (maxTotalContributions == 0) {
            return CrowdfundLifecycle.Invalid;
        }

        uint256 expiry_ = expiry;
        if (expiry_ == 0) {
            return CrowdfundLifecycle.Finalized;
        }

        if (block.timestamp >= expiry_) {
            if (totalContributions >= minTotalContributions) {
                return CrowdfundLifecycle.Won;
            } else {
                return CrowdfundLifecycle.Lost;
            }
        }

        return CrowdfundLifecycle.Active;
    }
```

## Mitigation
Ensure sanity checks on crowdfund duration not to allow crowdfunds to lock user balances. Max crowdfund duration should not last much more than a couple of years.
## lo-07 Party host can front-run emergencyExecute calls and disable emergency executions
## Impact
When the Party DAO attempts to call emergencyExecute at a PartyGovernance contract, a malicious host can evaluate the transaction's effects at the mempool and front-run it depending on the state changes it generates to cancel it's execution. This can be done by calling disableEmergencyExecutions right before the emergency execution and will cancel out all new executions.
## Proof of Concept
[Code snippet 1](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L388-L398)
```solidity
function disableEmergencyExecute() external {
        // Only the DAO or a host can call this.
        if (
            !party.isHost(msg.sender) &&
            _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET) != msg.sender
        ) {
            revert OnlyPartyDaoOrHostError(msg.sender);
        }
        emergencyExecuteDisabled = true;
        emit EmergencyExecuteDisabled();
    }
```
disableEmergencyExecute sets emergencyExecuteDisabled to true hence breaking the ability of the contract to handle emergencyExecutions irreversibly - as there are no functions that set this bool value back to false.
## Mitigation
Make sure emergency execution is defined once during construction/initialization and not arbitrarily later while the contract is running on-chain.