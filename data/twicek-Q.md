| Issue Number | Issue Name |
|--------------|------------|
| [L-01](#l-01-removing-the-onlydelegatecall-modifier-from-the-functions-in-partygovernancenft-allows-an-attacker-to-initialize-the-implementation-and-selfdestruct-it) | Removing the `onlyDelegateCall` modifier from the functions in `PartyGovernanceNFT` allows an attacker to initialize the implementation and selfdestruct it. |
| [L-02](#l-02-array-lengths-not-checked) | Array lengths not checked |
| [N-01](#n-01-the-party-address-passed-to-createerc20distribution-doesnt-have-to-be-payable) | The party address passed to `createErc20Distribution` doesn't have to be payable |


## [L-01] Removing the `onlyDelegateCall` modifier from the functions in `PartyGovernanceNFT` enable more attack vectors.

The initialize function has a `onlyInitialize` modifier which allows the `initialize` to only be called once:

[Implementation.sol#L29-L36](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/utils/Implementation.sol#L29-L36)
```solidity
    modifier onlyInitialize() {
        if (initialized) revert AlreadyInitialized();


        initialized = true;
        emit Initialized();


        _;
    }
```

[Party.sol#L39-L51](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/Party.sol#L39-L51)
```solidity
    function initialize(PartyInitData memory initData) external onlyInitialize {
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

The party protocol deployer will call initialize for the proxy, but the implementation storage will not be updated. Therefore an attacker will be able to call initialize on the implementation with his own parameters.

Specifically, calling initialize on the `Party` contract will also call the internal initialize functions of `PartyGovernanceNFT` and `PartyGovernance`, which could have led the attacker to set the `ProposalExecutionEngine`. But fortunately the contract can only be set by an hardcoded value from the `Globals` contract:

[PartyGovernance.sol#L284-L287](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L284-L287)
```solidity
        _initProposalImpl(
            IProposalExecutionEngine(_GLOBALS.getAddress(LibGlobals.GLOBAL_PROPOSAL_ENGINE_IMPL)),
            abi.encode(proposalEngineOpts)
        );
```

If it was not the case it could have been potentially possible for an attacker to call a function that has a delegatecall in the implementation contract like `cancel` or `_executeProposal` (now that there is no `onlyDelegateCall` modifier anymore). The attacker could have created a malicious cancel function in `ProposalExecutionEngine` with a selfdestruct, allowing him to selfdestruct the implementation.

I would recommend to block the ability to initialize the implementation as it create this kind of attack vector.

## [L-02] Array lengths not checked
If the length of the arrays are not required to be of the same length, user operations may not be fully executed due to a mismatch in the number of items iterated over, versus the number of items provided in the second array

```solidity
File: contracts/party/PartyGovernance.sol
706:     function execute(
             uint256 proposalId,
             Proposal memory proposal,
             IERC721[] memory preciousTokens,
             uint256[] memory preciousTokenIds,
             bytes calldata progressData,
             bytes calldata extraData
         ) external payable {
             _assertNotGloballyDisabled();
860:     function _executeProposal(
             uint256 proposalId,
             Proposal memory proposal,
             IERC721[] memory preciousTokens,
             uint256[] memory preciousTokenIds,
             uint256 flags,
             bytes memory progressData,
             bytes memory extraData
         ) private returns (bool completed) {
             // Setup the arguments for the proposal execution engine.
1147:     function _isPreciousListCorrect(
              IERC721[] memory preciousTokens,
              uint256[] memory preciousTokenIds
          ) private view returns (bool) {
              return preciousListHash == _hashPreciousList(preciousTokens, preciousTokenIds);
1154:     function _hashPreciousList(
              IERC721[] memory preciousTokens,
              uint256[] memory preciousTokenIds
          ) internal pure returns (bytes32 h) {
              assembly {
```
*GitHub*: [706](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L706) [860](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L860) [1147](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1147) [1154](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1154) 
<details>
<summary>More instances</summary>

```solidity
File: contracts/party/PartyGovernanceNFT.sol
81:     function _initialize(
            string memory name_,
            string memory symbol_,
            uint256 customizationPresetId,
            PartyGovernance.GovernanceOpts memory governanceOpts,
            ProposalStorage.ProposalEngineOpts memory proposalEngineOpts,
            IERC721[] memory preciousTokens,
            uint256[] memory preciousTokenIds,
            address[] memory authorities,
            uint40 rageQuitTimestamp_
        ) internal {
```
*GitHub*: [81](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L81) 
</details>the arrays are not required to be of the same length, user operations may not be fully executed due to a mismatch in the number of items iterated over, versus the number of items provided in the second array

## [N-01] The party address passed to `createErc20Distribution` doesn't have to be payable

[PartyGovernance.sol#L533-L542](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L533-L542)
```solidity
assert(tokenType == ITokenDistributor.TokenType.Erc20);
        IERC20(token).compatTransfer(address(distributor), amount);
        return
            distributor.createErc20Distribution(
                IERC20(token),
                Party(payable(address(this))),
                feeRecipient_,
                feeBps_
            );
    }
```