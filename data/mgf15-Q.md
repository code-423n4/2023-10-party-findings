the disableEmergencyExecute function Revoke the DAO's ability to call emergencyExecute 

```solidity
function emergencyExecute(
        address targetAddress,
        bytes calldata targetCallData,
        uint256 amountEth
    ) external payable {
        // Must be called by the DAO.
        if (_GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET) != msg.sender) {
            revert OnlyPartyDaoError(msg.sender);
        }
        // Must not be disabled by DAO or host.
        //@audit check if emergencyExecuteDisabled if true 
        if (emergencyExecuteDisabled) {
            revert OnlyWhenEmergencyActionsAllowedError();
        }
        (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
        if (!success) {
            res.rawRevert();
        }
        emit EmergencyExecute(targetAddress, targetCallData, amountEth);
    }
```
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

when emergencyExecuteDisabled set to true there is no way to set it to false this will lead the function `emergencyExecute()` no longer available after disableEmergencyExecute called 