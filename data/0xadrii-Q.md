# 0xadrii QA Report

## 1. Emergency execute can never be enabled once it is disabled [LOW]

### Lines of code

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L396

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L376

### Bug description

`ETHCrowdfundBase` includes an `emergencyExecute()` function, which allows the DAO to execute an arbitrary function call from the crowdfund contract. Taking a look at `ETHCrowdfundBase`, we can see that the emergency execution won’t be able to be triggered if the`emergencyExecuteDisabled` is set to true:

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

The`emergencyExecuteDisabled` flag is controlled by the `disableEmergencyExecute()` function. If such function is triggered by the DAO or the party host, `emergencyExecuteDisabled` will be set to true, and emergency executions will no longer be able to be triggered:

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

The problem with this approach is that there is no way to reset the `emergencyExecuteDisabled` flag to false if the DAO wants to perform an emergency execution. Once emergency execute is disabled, there is no `enableEmergencyExecute()` or similar function that allows emergency executions to take place.

### Impact

I believe this is a low vulnerability due to the fact that emergency executions should only take place in extreme situations, and it is not likely that the DAO will require to enable the emergency execution if it has already been disabled. However, it would be good to at least have the possibility to enable it.

### Tools used

Manual review

### Recommended Mitigation

As previously mentioned, add a permissioned `enableEmergencyExecute()` function that allows to reset the `emergencyExecuteDisabled` to false.

## 2. Initializing an ETH crowdfund should enable setting a delegate even if msg.value==0 [LOW]
### Lines of code

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L141

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L298

### Bug description

When initializing a crowdfund, a contribution will be performed if and only if `msg.value > 0`. This is not correct if we consider the fact that contributions with 0 amount are allowed to be performed, so that the solely purpose of the contribution is to change the delegate, without actually contributing any ether.

If we check the InitialETHCrowdfund initialization, we’ll be able to see that `_contribute()` will only be called when the transaction’s value is superior to 0:

```solidity
function initialize(
        InitialETHCrowdfundOptions memory crowdfundOpts,
        ETHPartyOptions memory partyOpts,
        MetadataProvider customMetadataProvider,
        bytes memory customMetadata
    ) external payable onlyInitialize {
        ...

        // If the deployer passed in some ETH during deployment, credit them
        // for the initial contribution.
        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
        if (initialContribution > 0) {
            // If this contract has ETH, either passed in during deployment or
            // pre-existing, credit it to the `initialContributor`.
            _contribute(
                crowdfundOpts.initialContributor,
                crowdfundOpts.initialDelegate,
                initialContribution,
                0,
                ""
            );
        }

				...
    }
```

This is not correct, since the crowdfund deployer could try to add a delegate without actually sending any ether, which is supported by `_contribute()`:

```solidity
function _contribute(
        address payable contributor,
        address delegate,
        uint96 amount,
        uint256 tokenId,
        bytes memory gateData
    ) private returns (uint96 votingPower) {
        // Require a non-null delegate.
        if (delegate == address(0)) {
            revert InvalidDelegateError();
        }
			
				...

        votingPower = _processContribution(contributor, delegate, amount);

        // OK to contribute with zero just to update delegate.
        if (amount == 0) return 0;

        ...
    }
```

### Impact

Low, since it is a logical issue that does not impact funds and supposes an improbable situation.

### Proof of Concept

As stated in the previous sections, we can easily see how `initialize()` directly skips contributing if `initialContribution` (which is a casted `msg.value`) is 0:

```solidity
function initialize(
        InitialETHCrowdfundOptions memory crowdfundOpts,
        ETHPartyOptions memory partyOpts,
        MetadataProvider customMetadataProvider,
        bytes memory customMetadata
    ) external payable onlyInitialize {
        ...

        // If the deployer passed in some ETH during deployment, credit them
        // for the initial contribution.
        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
        if (initialContribution > 0) {
            // If this contract has ETH, either passed in during deployment or
            // pre-existing, credit it to the `initialContributor`.
            _contribute(
                crowdfundOpts.initialContributor,
                crowdfundOpts.initialDelegate,
                initialContribution,
                0,
                ""
            );
        }

				...
    }
```

### Tools used

Manual review

### Recommended Mitigation

Allow `_contribute()` to be executed even if the crowdfund deployer does not send a `msg.value` in the initializing transaction.


## 3. InitialETHCrowdfund allows a party to have 0 hosts [LOW]

### Lines of code

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L108

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L295

### Bug description

It is possible for a party to have 0 hosts if an InitialETHCrowdfund is created. As we can see in the party initialization, the hosts are directly set without checking if `govOpts.hosts.length` is greater than 0:

```solidity
// PartyGovernance.sol
function _initialize(
        GovernanceOpts memory govOpts,
        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
        IERC721[] memory preciousTokens,
        uint256[] memory preciousTokenIds
    ) internal virtual {
	      ... 
        
				numHosts = uint8(govOpts.hosts.length);
        

				// Set fees.
        feeBps = govOpts.feeBps;
        feeRecipient = govOpts.feeRecipient;
        // Set the precious list.
        _setPreciousList(preciousTokens, preciousTokenIds);
        // Set the party hosts.
        if (govOpts.hosts.length > type(uint8).max) {
            revert TooManyHosts();
        }
        for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
            isHost[govOpts.hosts[i]] = true;
        }
    }
```

And the InitialETHCrowdfund initialization doesn’t throw an error if the number of hosts is 0:

```solidity

// InitialETHCrowdfund.sol
    function initialize(
        InitialETHCrowdfundOptions memory crowdfundOpts, 
        ETHPartyOptions memory partyOpts,
        MetadataProvider customMetadataProvider,
        bytes memory customMetadata
    ) external payable onlyInitialize { //@audit not true. `onlyInitialize` does not enforce initialize to be called via delegatecall, opening a potential attack for an implementation hijacking
        // Create party the initial crowdfund will be for.
        Party party_ = _createParty(partyOpts, customMetadataProvider, customMetadata);
  
        // Initialize the crowdfund.
        _initialize( 
            ETHCrowdfundOptions({
                party: party_,
                initialContributor: crowdfundOpts.initialContributor,
                initialDelegate: crowdfundOpts.initialDelegate,
                minContribution: crowdfundOpts.minContribution,
                maxContribution: crowdfundOpts.maxContribution,
                disableContributingForExistingCard: crowdfundOpts
                    .disableContributingForExistingCard,
                minTotalContributions: crowdfundOpts.minTotalContributions,
                maxTotalContributions: crowdfundOpts.maxTotalContributions,
                exchangeRateBps: crowdfundOpts.exchangeRateBps,
                fundingSplitBps: crowdfundOpts.fundingSplitBps,
                fundingSplitRecipient: crowdfundOpts.fundingSplitRecipient,
                duration: crowdfundOpts.duration,
                gateKeeper: crowdfundOpts.gateKeeper,
                gateKeeperId: crowdfundOpts.gateKeeperId
            })
        );

        // If the deployer passed in some ETH during deployment, credit them
        // for the initial contribution.
        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
        if (initialContribution > 0) {  //@audit This should be able to be triggered even if msg.value == 0, because contributions allow to change delegate even if value passed is 0!
            // If this contract has ETH, either passed in during deployment or
            // pre-existing, credit it to the `initialContributor`.
            _contribute(
                crowdfundOpts.initialContributor,
                crowdfundOpts.initialDelegate,
                initialContribution,
                0,
                ""
            );
        }

        // Set up gatekeeper after initial contribution (initial always gets in).
        gateKeeper = crowdfundOpts.gateKeeper;
        gateKeeperId = crowdfundOpts.gateKeeperId; 
    }
```

If no hosts are set, some functions that can only be triggered by hosts (such as `veto()` or `rageQuit()`) will not be able to be called, effectively causing a DoS in such functions.

### Impact

I consider this issue has a low impact due to the fact that it is improbable that a crowdfund deployer sets 0 hosts in a crowdfund, although a malicious crowdfund deployer can decide to set 0 hosts in order to prevent proposals from being vetoed, or to also restrict access to other host-permissioned functions.

### Proof of Concept

As we can see in the following code block, there is no error thrown if the hosts length is 0, and in other part of the initialization of the crowdfund:

```solidity

// InitialETHCrowdfund.sol
    function initialize(
        InitialETHCrowdfundOptions memory crowdfundOpts, 
        ETHPartyOptions memory partyOpts,
        MetadataProvider customMetadataProvider,
        bytes memory customMetadata
    ) external payable onlyInitialize { //@audit not true. `onlyInitialize` does not enforce initialize to be called via delegatecall, opening a potential attack for an implementation hijacking
        // Create party the initial crowdfund will be for.
        Party party_ = _createParty(partyOpts, customMetadataProvider, customMetadata);
  
        // Initialize the crowdfund.
        _initialize( 
            ETHCrowdfundOptions({
                party: party_,
                initialContributor: crowdfundOpts.initialContributor,
                initialDelegate: crowdfundOpts.initialDelegate,
                minContribution: crowdfundOpts.minContribution,
                maxContribution: crowdfundOpts.maxContribution,
                disableContributingForExistingCard: crowdfundOpts
                    .disableContributingForExistingCard,
                minTotalContributions: crowdfundOpts.minTotalContributions,
                maxTotalContributions: crowdfundOpts.maxTotalContributions,
                exchangeRateBps: crowdfundOpts.exchangeRateBps,
                fundingSplitBps: crowdfundOpts.fundingSplitBps,
                fundingSplitRecipient: crowdfundOpts.fundingSplitRecipient,
                duration: crowdfundOpts.duration,
                gateKeeper: crowdfundOpts.gateKeeper,
                gateKeeperId: crowdfundOpts.gateKeeperId
            })
        );

        // If the deployer passed in some ETH during deployment, credit them
        // for the initial contribution.
        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
        if (initialContribution > 0) {  //@audit This should be able to be triggered even if msg.value == 0, because contributions allow to change delegate even if value passed is 0!
            // If this contract has ETH, either passed in during deployment or
            // pre-existing, credit it to the `initialContributor`.
            _contribute(
                crowdfundOpts.initialContributor,
                crowdfundOpts.initialDelegate,
                initialContribution,
                0,
                ""
            );
        }

        // Set up gatekeeper after initial contribution (initial always gets in).
        gateKeeper = crowdfundOpts.gateKeeper;
        gateKeeperId = crowdfundOpts.gateKeeperId; 
    }

// PartyGovernance.sol
function _initialize(
        GovernanceOpts memory govOpts,
        ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
        IERC721[] memory preciousTokens,
        uint256[] memory preciousTokenIds
    ) internal virtual {
	      ... 
        
				numHosts = uint8(govOpts.hosts.length);
        

				// Set fees.
        feeBps = govOpts.feeBps;
        feeRecipient = govOpts.feeRecipient;
        // Set the precious list.
        _setPreciousList(preciousTokens, preciousTokenIds);
        // Set the party hosts.
        if (govOpts.hosts.length > type(uint8).max) {
            revert TooManyHosts();
        }
        for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
            isHost[govOpts.hosts[i]] = true;
        }
    }
```

### Tools used

Manual review

### Recommended Mitigation

Throw an error if the number of hosts is 0. It can be done in a similar way as it is done in [CollectionBatchBuyCrowdfund](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/CollectionBatchBuyCrowdfund.sol#L104), where the number of hosts is actually verified:

```solidity
// CollectionBatchBuyCrowdfund.sol
function initialize(
        CollectionBatchBuyCrowdfundOptions memory opts
    ) external payable onlyInitialize {
        if (opts.governanceOpts.hosts.length == 0) {
            revert MissingHostsError();
        }
        ...
    }
```

## 4. No validation for fundingSplitBps and exchangeRateBps allows creator to set bps to a huge amount, taking all funds [LOW]

### Lines of code

https://github.com/code-423n4/2023-05-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L109

https://github.com/code-423n4/2023-05-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L108

https://github.com/code-423n4/2023-05-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L148

https://github.com/code-423n4/2023-05-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L150

### Bug description

There is no check ensuring maximum values for `fundingSplitBps` and `exchangeRateBps`, which makes such values be susceptible of being set to a high value, leaving users without an unfair or even null portion of their contributions. We can see how they are directly set when a crowdfund is initialized, and they are not verified in any other part of the crowdfund creation flow:

```solidity

function _initialize(ETHCrowdfundOptions memory opts) internal {
        ...
        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
        exchangeRateBps = opts.exchangeRateBps;
        // Set the funding split and its recipient.
        fundingSplitBps = opts.fundingSplitBps;
        ...
    }
```

Even `exchangeRateBps` is validated to not be 0, but there is no a real maximum value for such parameters.

Because `fundingSplitBps` and `exchangeRateBps` are in BPS (Basis Points), they could be set to a value of 10000, effectively making users lose all of their contributions or voting power because of cutting all of their contributed amounts in the percentages calculations, or even to a value higher than 10000, making all percentages in the contract be calculated improperly.

### Impact

Although the scenario where such values could be set and this supposes a real impact for crowdfund contributors, the probability of performing such action is low and users would rapidly notice the issue. Hence, setting a **low** impact.

### Proof of Concept

We can see how InitialETHCrowdfund’s initialization process does not validate bps parameters in any moment to set maximum values:

```solidity
// InitialETHCrowdfund.sol
function initialize(
        InitialETHCrowdfundOptions memory crowdfundOpts,
        ETHPartyOptions memory partyOpts
    ) external payable onlyConstructor {
        ...

        // Initialize the crowdfund.
        _initialize(
            ETHCrowdfundOptions({
                party: party_,
                initialContributor: crowdfundOpts.initialContributor,
                initialDelegate: crowdfundOpts.initialDelegate,
                minContribution: crowdfundOpts.minContribution,
                maxContribution: crowdfundOpts.maxContribution,
                disableContributingForExistingCard: crowdfundOpts
                    .disableContributingForExistingCard,
                minTotalContributions: crowdfundOpts.minTotalContributions,
                maxTotalContributions: crowdfundOpts.maxTotalContributions,
                exchangeRateBps: crowdfundOpts.exchangeRateBps,
                fundingSplitBps: crowdfundOpts.fundingSplitBps,
                fundingSplitRecipient: crowdfundOpts.fundingSplitRecipient,
                duration: crowdfundOpts.duration,
                gateKeeper: crowdfundOpts.gateKeeper,
                gateKeeperId: crowdfundOpts.gateKeeperId
            })
        );

        ...
    }

// ETHCrowdfundBase.sol
function _initialize(ETHCrowdfundOptions memory opts) internal {
        ...
				 // Set the exchange rate.
        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
        exchangeRateBps = opts.exchangeRateBps;
        // Set the funding split and its recipient.
        fundingSplitBps = opts.fundingSplitBps;
       ...
    }
```

### Tools used

Manual review

### Recommended Mitigation

Add a validation for bps parameters to not exceed a certain amount. Although only checking if the bps amounts are higher  than 10000 instead of thinking about a reasonable value where users would not be greatly impacted by fees, it would restrict flexibility for the crowdfund. Hence, at least ensuring bps params are lower than 10000 should be added.

## 5. Signature malleability in OffChainSignatureValidator.sol [LOW]
### Lines of code

https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol#L34-L37

## Bug description

Solidity’s built-in `ecrecover` function is vulnerable to signature malleability, meaning the same address can be obtained as output by passing two different signatures for the same signed hash. As stated in EIP-2: “All transaction signatures whose s-value is greater than `secp256k1n/2` are now considered invalid. The ECDSA recover precompiled contract remains unchanged and will keep accepting high s-values; this is useful e.g. if a contract recovers old Bitcoin signatures.”.

Because the OffChainSignatureValidator does not perform any validation on the extracted signature values, `isValidSignature()` is susceptible to signature malleability:

```solidity
// OffChainSignatureValidator.sol
function isValidSignature(bytes32 hash, bytes memory signature) external view returns (bytes4) {
        uint8 v;
        bytes32 r;
        bytes32 s;
        assembly {
            // First word of signature after size contains r
            r := mload(add(signature, 0x20))
            s := mload(add(signature, 0x40))
            // v is one byte which starts after s. type is uint8 so extra data will be ignored
            v := mload(add(signature, 0x41))
        } 
		
        ...
    }
```

## Impact

Low, due to the fact that the current code is expected to allow valid to be reused with the goal of signing simple messages (not EIP-712 signatures, for example). The signature being malleable won’t change the output address from `ecrecover`, which is expected to have a required voting power in order to allow the signature to be valid, and the `hash` won’t change either. For all of these reasons, I consider this a low vulnerability.

## Proof of Concept

We can see how output from `ecrecover` is never checked in OffChainSignatureValidator’s `isValidSignature()`:

```solidity
function isValidSignature(bytes32 hash, bytes memory signature) external view returns (bytes4) {
        uint8 v;
        bytes32 r;
        bytes32 s;
        assembly {
            // First word of signature after size contains r
            r := mload(add(signature, 0x20))
            s := mload(add(signature, 0x40))
            // v is one byte which starts after s. type is uint8 so extra data will be ignored
            v := mload(add(signature, 0x41))
        } 

        bytes memory message;
        assembly {
            // Raw message data begins after v. Overwriting part of s and v with size of `message`
            message := add(signature, 0x41)
            mstore(message, sub(mload(signature), 0x41))
        }

        // Recreate the message pre-hash from the raw data
        bytes memory encodedPacket = abi.encodePacked(
            "\x19Ethereum Signed Message:\n",
            Strings.toString(message.length),
            message
        );
        if (keccak256(encodedPacket) != hash) {
            revert MessageHashMismatch();
        }

        Party party = Party(payable(msg.sender));
        address signer = ecrecover(hash, v, r, s);
        uint96 signerVotingPowerBps = party.getVotingPowerAt(signer, uint40(block.timestamp)) *
            10000;  

        if (signerVotingPowerBps == 0 && party.balanceOf(signer) == 0) {
            // Must own a party card or be delegatated voting power
            revert NotMemberOfParty();
        }

        uint96 totalVotingPower = party.getGovernanceValues().totalVotingPower;
        uint96 thresholdBps = signingThersholdBps[party];

        // Either threshold is 0 or signer votes above threshold
        if (
            thresholdBps == 0 ||
            (signerVotingPowerBps > totalVotingPower &&
                signerVotingPowerBps / totalVotingPower >= thresholdBps)
        ) {
            return IERC1271.isValidSignature.selector;
        }

        revert InsufficientVotingPower();
    }
```

## Tools used

Manual review

## Recommended Mitigation

Validate the `s` and `v` output from `ecrecover`, or consider using [OpenZeppelin’s ECDSA library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol).

## 6. Implementations can be hijacked due to not enforcing initialization [LOW]

### Lines of code

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/Party.sol#L39

### Bug description

One of the changes performed in the current version of the Party protocol was to utilize ERC-1167 proxy clones. As such, implementations will be cloned into ERC-1167 proxies, which will then be initialized by executing the `initialize()` function from the implementation. 

The problem is that while previous Party protocol versions restricted `initialize()` to only be called from a constructor (i.e in the proxy creation), now they have been changed to an `onlyInitialize()` modifier that does not allow `initialize()` to be triggered more than once:

```diff
function initialize(PartyInitData memory initData) 
external 
- onlyConstructor
+ onlyInitialize {
        PartyGovernanceNFT._initialize(
            initData.options.name,
            initData.options.symbol,
            initData.options.customizationPresetId,
            initData.options.governance,
            initData.options.proposalEngine,
            initData.preciousTokens,
            initData.preciousTokenIds,
            initData.authorities,
            initData.rageQuitTimestamp
        );
    }
```

Although this fulfills its purpose (not being able to initialize a proxy more than once), it adds a vulnerability where the implementations can be initialized by anyone.

Although none of the current implementations from any contract in the protocol allows for the well-known selfdestruct implementation attack, care should be taken by properly initializing the implementation as well.

### Impact

Low, implementations can be hijacked but won’t have a greater impact in the proxies holding the data.

### Tools used

Manual review

### Recommended Mitigation

Consider setting the initializer to `true` variable in the constructor, so that `initialize()` can’t be triggered in the implementation.