# L-01: Host can denounce his role completely 
the `abdicateHost` function is for Transferring  party host status to another. But using this function a host can donounce his role by setting new party host to zero adddress ! 

This concerns about a scenario  where there's no host in a party ! Which is not intended as some functionalities requires the host to be executed . 
```solidity 

function abdicateHost(address newPartyHost) external { //@q no options for adding a host ? 
        _assertHost();
        // 0 is a special case burn address.
        if (newPartyHost != address(0)) {
            // Cannot transfer host status to an existing host.
            if (isHost[newPartyHost]) {
                revert InvalidNewHostError();
            }
            isHost[newPartyHost] = true;
        } else {//@ci there should be atleast one host ig ! A host cannot just leave a party by itself ? 
            // Burned the host status
            --numHosts; //@audit could be zero ! 
        }
        isHost[msg.sender] = false;
        emit HostStatusTransferred(msg.sender, newPartyHost);
    }
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L457
### recommendation :
Last host should not leave the party ! 
Revert if there's only one host left .. 

# L-02: Comments state that pre-existing ETH can be used but it can’t
It is possible to provide an initial contribution to  InitialETHCrowdfund contracts.

The initial contribution is processed when the initialize function is called.

In both contracts it is stated that pre-existing ETH is used for the initial contribution (i.e. ETH that is owned by the contract but not sent along with the call to the initialize function):
```solidity 
uint96 initialContribution = msg.value.safeCastUint256ToUint96();

```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L140
### recommendation :
Remove those lines of comments if thats not intended  . 
```diff 
 
-        // If the deployer passed in some ETH during deployment, credit them
-        // for the initial contribution.
         uint96 initialContribution = msg.value.safeCastUint256ToUint96();
         if (initialContribution > 0) {
-            // If this contract has ETH, either passed in during deployment or
-            // pre-existing, credit it to the `initialContributor`.
+            // credit msg.value to the `initialContributor`.
             _contribute(
                 crowdfundOpts.initialContributor,
                 crowdfundOpts.initialDelegate,
```
# L-03 : Minted tokenid is not returned 
The `_contribute ` function mints a token to contributor if tokeniID is 0 . But Minted TokenID returned by the mint function is not chcecked and returned to show to the user . This is a best practise 
```solidity 

        party.mint(contributor, votingPower, delegate);
```
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L302
### recommendation :
Catch the return value and return . 
