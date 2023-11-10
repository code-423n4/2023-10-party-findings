## ETHCrowdfundBase
1) there's no re-enable emergencyExecute function inside the contract that re-enable that set the "emergencyExecuteDisable" variable to false, i suggest to change the "disableEmergencyExecute" function using a bool value as param and set the "emergencyExecuteDisabled" variable using the param value
https://github.com/PartyDAO/party-protocol/blob/37dae4292dde2547a3b1ced8a041f926e1b37d58/contracts/crowdfund/ETHCrowdfundBase.sol#L388-L399
2) line 166 should be checked that fundingSplitBps will be even lower to 1e4,because if not,that will cause underflow problems in several lines and functions calculations,like (266,287)
https://github.com/PartyDAO/party-protocol/blob/37dae4292dde2547a3b1ced8a041f926e1b37d58/contracts/crowdfund/ETHCrowdfundBase.sol#L166
https://github.com/PartyDAO/party-protocol/blob/37dae4292dde2547a3b1ced8a041f926e1b37d58/contracts/crowdfund/ETHCrowdfundBase.sol#L266
https://github.com/PartyDAO/party-protocol/blob/37dae4292dde2547a3b1ced8a041f926e1b37d58/contracts/crowdfund/ETHCrowdfundBase.sol#L287
3) ## few wei will be lost on refund due to small calculation problem
it is a small difference between the actual refund and the current refund of about 4 wei in case the crowdfund was created with a fundingSplitRecipient, fundingSplitBps and exchangeRateBps
### Poc
```
function test_powerToContribution(uint256 commissions,uint256 exchangeRateBpss)public{
        uint256 amount = 10e18;
        vm.assume(commissions<5e3 && commissions>100 );
        vm.assume(exchangeRateBpss<10000 && exchangeRateBpss>100);
        address fundingSplitRecipientAddress = _randomAddress();
        uint256 startingAmount = amount;
        InitialETHCrowdfund crowdfund = _createCrowdfund(
            CreateCrowdfundArgs({
                initialContribution: 0,
                initialContributor: payable(address(0)),
                initialDelegate: address(0),
                minContributions: 0,
                maxContributions: type(uint96).max,
                disableContributingForExistingCard: false,
                minTotalContributions: 10 ether,
                maxTotalContributions: 500 ether,
                duration: 1 days,
                exchangeRateBps: uint16(exchangeRateBpss),
                fundingSplitBps: uint16(commissions),
                fundingSplitRecipient: payable(fundingSplitRecipientAddress),
                gateKeeper: IGateKeeper(address(0)),
                gateKeeperId: bytes12(0)
            }),
            false
        );
        crowdfund.initialize(crowdfundOpts, partyOpts, MetadataProvider(address(0)), "");
        address payable fundingSplitRecipient_ = crowdfund.fundingSplitRecipient();
        uint16 fundingSplitBps_ = crowdfund.fundingSplitBps();
        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
            amount = (startingAmount * (exchangeRateBpss - fundingSplitBps_)) / exchangeRateBpss;
        }
        uint256 votingPower = (amount * crowdfund.exchangeRateBps()) / exchangeRateBpss;

        uint256 resultAmount = (votingPower * exchangeRateBpss) / crowdfund.exchangeRateBps();
        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
            resultAmount = (resultAmount * exchangeRateBpss) / (exchangeRateBpss - fundingSplitBps_);
        }
        
        address member = _randomAddress();
        vm.deal(member, amount);

        vm.startPrank(member);
        vm.expectEmit(true, false, false, true);
        emit Contributed(member, member, amount, member);
        crowdfund.contribute{ value: amount }(member, "");
        console.log(address(crowdfund).balance);
        vm.stopPrank();
        vm.warp(block.timestamp + 1 days);
        vm.startPrank(member);
        uint256 resussslt = crowdfund.refund(1);
        vm.stopPrank();
        assertEq(resussslt,10e18-((commissions*10e18)/exchangeRateBpss));
    }
```
add this into the initialETHCrowdfund.t.sol test file
### Mitigation
could be resolved by seeing the hypothetical refund value,calculated for,for example, by an internal function and compare it to the actual value,get the few wei difference and use the new amount to get the refund