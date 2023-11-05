### `maxContribution` should track maximum contribution that can be made by an address in a crowdfund

[Link to code](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L98)

The state variable `ETHCrowdfundBase:maxContribution;` supposedly should track the maximum contribution that can be made by a particular address. But, it's just checking for the maximum contribution that can be made in a single transaction, allowing the member to contribute more up to `maxTotalContributions` through multiple contribute transactions.

I assume, it is intended to cap the maximum contribution/voting power a particular address can gain in a crowdfund. Also, using this functionality a host can limit the voting power that a single address can have, which is a good use case of it.

If that's not the motive then the value can simply be a constant `type(uint96).max`.

### Mitigation

The solution for this is to use a mapping that tracks the contribution made by an address `mapping(address => uint96) maxContribution;` in `ETHCrowdfundBase:_processMapping()`.

### POC

In the below POC `maxContribution` is set to 1 ether. Even though in a single contribution transaction only 1 ether can be sent, a member can send multiple contribution transactions bypassing the `maxContribution` value.

```solidity
    function test_maxContribution() public {
        InitialETHCrowdfund crowdfund = _createCrowdfund(
            CreateCrowdfundArgs({
                initialContribution: 0,
                initialContributor: payable(address(0)),
                initialDelegate: address(0),
                minContributions: 0,
                maxContributions: 1 ether, //Max-contribution set to 1 ether.
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

        address member = _randomAddress();
        vm.deal(member, 10 ether);

        // Contribute
        vm.startPrank(member);

        vm.expectEmit(true, false, false, true);
        emit Contributed(member, member, 1 ether, member);
        //Member doing multiple contribution
        crowdfund.contribute{ value: 1 ether }(member, "");
        crowdfund.contribute{ value: 1 ether }(member, "");
        crowdfund.contribute{ value: 1 ether }(member, "");
        crowdfund.contribute{ value: 1 ether }(member, "");
        crowdfund.contribute{ value: 1 ether }(member, "");
        vm.stopPrank();

        console.log('Balance of member', party.balanceOf(member));
        console.log('Total minted voting power', party.mintedVotingPower());

        assertEq(address(crowdfund).balance, 0 ether);
        assertEq(crowdfund.totalContributions(), 5 ether);
        assertEq(crowdfund.delegationsByContributor(member), member);
    }

```

**Logs**

```
  Balance of member 5
  Total minted voting power 5000000000000000000

```