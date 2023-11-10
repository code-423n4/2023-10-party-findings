
# [L-01] `InitialETHCrowdfund::batchContribute` does not check the sent ETH amount

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



# [L-02] Missing function input validation inside the `InitialETHCrowdfund::batchContribute` function

In the function `batchContibute` we are sending `BatchContributeArgs calldata args` where we have an array of parameters like `tokenIds`, `values`, and `gateDatas`. Inside the function, we are iterating over these array values and passing them one by one to the `_contribute` function. The problem here is that there is no input validation for these parameters, and we are not making sure that they have the same length. There is actually one comment inside the `BatchContributeArgs` struct: `The contribution amounts in wei. The length of this array must be equal to the length of 'tokenIds'.`

# Impact

The missing input validation or different checks on the struct array elements like `tokenIds`, `values`, and `gateDatas` could result in mismatches inside the `batchContribute` function. This will potentially lead to arrays with different lengths being passed as a function parameter of the struct, which is violating the expected structure and causing `Index out of bounds` errors.

# Proof of Concept

```solidity
    function test_batchContribute_fails_when_values_array_is_with_different_length()
        public
    {
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
        vm.deal(member, 10 ether);

        // Batch contribute
        vm.prank(member);
        uint256[] memory tokenIds = new uint256[](3);

        // @audit Here we are declaring an array with only one element so the length of it
        // will be different from tokenIds and gateDatas arrays
        // the test will fail with: `[FAIL. Reason: Index out of bounds]`
        uint96[] memory values = new uint96[](1);
        for (uint256 i; i < 1; ++i) {
            values[i] = 1 ether;
        }
        bytes[] memory gateDatas = new bytes[](3);
        uint96[] memory votingPowers = crowdfund.batchContribute{
            value: 10 ether
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

Make sure that all of the array properties (tokenIds, values, gateDatas) from the `BatchContributeArgs` struct have the same number of items. This is even mentioned as a comment inside the `BatchContributeArgs` struct: `The contribution amounts in wei. The length of this array must be equal to the length of 'tokenIds'.`

