
# `InitialETHCrowdfund::batchContribute` does not check the sent ETH amount

In the function `batchContibute` we are sending `BatchContributeArgs calldata args` where one of the parameters is an array of values. This array of values represents each contribution amount sent to be used aftewards by the `_contribute` function. Also, we are sending ETH, which is being stored later in the `ethAvailable` variable, like this: `uint256 ethAvailable = msg.value;`. Now, in a few lines, when we are below, we are subtracting `values[i]` from the `ethAvailable`. The problem is that we are not checking if we have enough ETH that was sent.

# Impact

The sent transaction with insufficient ETH will revert, due to an arithmetic overflow or underflow when attempting to process the underfunded contributions.

# Proof of Concept

This is a test that initiali sends less amount of ETH so the test here can fail with the error message `[FAIL. Reason: Arithmetic over/underflow]`.

```solidity
  function test_batchContribute_low_eth_amount() public {
        InitialETHCrowdfund crowdfund = _createCrowdfund(
            CreateCrowdfundArgs({
                initialContribution: 0,
                initialContributor: payable(address(0)),
                initialDelegate: address(0),
                minContributions: 0,
                maxContributions: type(uint96).max,
                disableContributingForExistingCard: false,
                minTotalContributions: 1 ether,
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

        address member = _randomAddress();
        // @audit Here we set two ETH and below we will pass an array with length 3
        // and each value will be with 1 ETH (total of 3 ETH), but we are will pass only 2 ETH
        vm.deal(member, 2 ether);

        // Batch contribute
        vm.prank(member);
        uint256[] memory tokenIds = new uint256[](3);
        uint96[] memory values = new uint96[](3);
        for (uint256 i; i < 3; ++i) {
            values[i] = 1 ether;
        }
        bytes[] memory gateDatas = new bytes[](3);
        uint96[] memory votingPowers = crowdfund.batchContribute{
        // @audit Here we are passing only 2 ETH (total value of values array is 3 ETH)
        // This will revert with: [FAIL. Reason: Arithmetic over/underflow]
            value: 2 ether
        }(
            InitialETHCrowdfund.BatchContributeArgs({
                tokenIds: tokenIds,
                delegate: member,
                values: values,
                gateDatas: gateDatas
            })
        );
    }
```

# Tools Used

Foundry, Manual Review

# Recommendations

Consider implementing checks to the logic inside the `batcgContribute` function to make sure that the `ethAvailable` is more or equal to the values that are being sent as an amount representing the contribution sum.
