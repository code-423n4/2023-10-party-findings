# `transferEth()` should be used instead of `transfer()` on `batchContribute()`
### Severity: Low
## Summary
Executing `batchContribute` reverts in some specific cases when user is a smart contract.
## Impact
Using `batchContribute()` we can delegate the voting power for all contributions to an address which is a smart contract - for example a multisig wallet.
The use of  `transfer()` function will make the transaction fail when:
1. The voting power for all contributions is delegated to a smart contract address that does not implement a payable function.
2. The voting power for all contributions is delegated to a smart contract address that does implement a payable receive which uses more than 2300 gas unit.

Using higher than 2300 gas might be mandatory for some multisig wallets.
## Proof of Concept
Use case when the the receive/fallback function of the smart contract uses more than 2300 gas.

1. First we have to create our contract to which all the voting power will be delegated to. A real-life example would be a multisig wallet.
```
// SPDX-License-Identifier: GPL-3.0
pragma solidity 0.8.20;

import "forge-std/Test.sol";

contract DummyContract {
    
    constructor() payable {}
    
    /**
     * Example when receive functions uses more than 2300 gas.
     * Might be a multisig walltet. 
     * 
     * @notice if the receive/fallaback function is not 
     * implemented the transaction will revert using transfer()
     * Use transferEth() instead.
     */
    receive() external payable {
        uint startgas = gasleft();
        uint256 sum = 0;
        // for the purpose of the example
        // dummy code using more than 2300 gas
        for (uint i = 1; i < 15; i++) {
            sum += i;
        }
        uint usedGas = startgas - gasleft();
        console.log("UsedGas", usedGas);
    }

    function onERC721Received(
        address operator,
        address from,
        uint256 tokenId,
        bytes calldata data
    ) external returns (bytes4) {
        return DummyContract.onERC721Received.selector;
    }

}
```
2. Add new test `test_batchContribute_revertsOnSmartContract()` in `InitialETHCrowdfundTestBase.t.sol`. We also have to import `DummyContract`  on top of the file.

```
function test_batchContribute_revertsOnSmartContract() public {
        InitialETHCrowdfund crowdfund = _createCrowdfund(
            CreateCrowdfundArgs({
                initialContribution: 0,
                initialContributor: payable(address(0)),
                initialDelegate: address(0),
                minContributions: 0,
                maxContributions: type(uint96).max,
                disableContributingForExistingCard: false,
                minTotalContributions: 3 ether,
                maxTotalContributions: 5 ether,
                duration: 7 days,
                exchangeRateBps: 1e4,
                fundingSplitBps: 0,
                fundingSplitRecipient: payable(address(0)),
                gateKeeper: IGateKeeper(address(0)),
                gateKeeperId: bytes12(0)
            })
        );
        Party party = crowdfund.party();

        // it might be a multisig wallet
        dummyContractMember = new DummyContract{value: 4 ether}();
        // address member = _randomAddress();
        // vm.deal(member, 4 ether);

        // Batch contribute
        vm.prank(address(dummyContractMember));
        uint256[] memory tokenIds = new uint256[](3);
        uint96[] memory values = new uint96[](3);
        for (uint256 i; i < 3; ++i) {
            values[i] = 1 ether;
        }
        bytes[] memory gateDatas = new bytes[](3);
        vm.expectRevert();
        uint96[] memory votingPowers = crowdfund.batchContribute{ value: 4 ether }(
            InitialETHCrowdfund.BatchContributeArgs({
                tokenIds: tokenIds,
                delegate: address(dummyContractMember),
                values: values,
                gateDatas: gateDatas
            })
        );
        assertEq(address(dummyContractMember).balance, 4 ether); // it should be 1 ETH in case it does not revert
    }
```
3. Run the test with
```
forge test --match-path test/crowdfund/InitialETHCrowdfund.t.sol --match-test test_batchContribute_revertsOnSmartContract -vv
```
**Result**: transaction reverts.
## Recommendation
Use `transfertEth()` which is part of `LibAddress` library instead of `transfer()`.
```
if (ethAvailable > 0) payable(msg.sender).transferEth(ethAvailable);
```
It is also valid to use `call()` as it is suggested in the bot race, but following the good design pattern the better approach is to use `transferEth()`.

Additionally this is a very well known issue and it’s not a good practice to use the deprecated `transfer()` function.