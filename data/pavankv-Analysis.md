
Analysis Report :-

**Index of table**
| Sl.no  | Particulars  |
|---|---|
| 1   | Overview  |
| 2  | Architecture view(Diagram)  |
| 3  | Approach taken in evaluating the codebase  |
| 4  | Centralization risks  |
| 5 | Mechanism review  |
| 6  | Recommendation   |
| 7  | Hours spend  |


## 1. Overview 
PartyDAO's first product is PartyBid, a platform for collective bidding on NFTs. PartyBid lets people pool their capital together to purchase NFTs as a team. Anyone can create or join a Party to collectively bid in an NFT auction or purchase an NFT that's on sale at a fixed price.Party DAOs offer a number of benefits over traditional party management structures. First, Party DAOs are more transparent and democratic. All members of the DAO have the right to vote on decisions that affect the party. Second, Party DAOs are more efficient. Smart contracts can automate many of the tasks that are typically associated with party management, such as collecting dues, distributing funds, and organizing events. Third, Party DAOs are more secure. Smart contracts are immutable, which means that they cannot be tampered with. This makes it very difficult for malicious actors to steal funds or disrupt the party's operations.PartyDAO is a good example of how crypto can be used to build new and innovative products and services. It is also a good example of how crypto can be used to create more decentralized and democratic organizations.

**Note :-**
This report suggests the best recommendations that the Party DAO team can adopt to get rid of the centralized risk associated with single-host parties and compromised hosts. This will help to build a transparent and secure ecosystem in the perception of outsiders.

## 2. Architecture View
![FlowChart](https://user-images.githubusercontent.com/69415766/282084189-24eef2cb-f291-44f0-9f53-8a9f1df0004a.png)

Visit the link to see full diagram.
https://lucid.app/lucidchart/8dd3c164-e34d-437a-939b-81512a795a6e/edit?page=0_0&invitationId=inv_5a80f4ab-4878-4c7a-bc99-e44ea1fc80f9#


## 3 . Approach taken in evaluating the codebase
We approached manual code review by looking into each space in the scoped contract and decided to explain some core functional contracts.it is important to note that manual code reviews can be very time-consuming.

The main components of PArty DAO are 
1.InitialETHCrowdfund.sol
2.ETHCrowdfundBase.sol
3.PartyGovernance.sol
4.PartyGovernanceNFT.sol
5.ProposalExecutionEngine.sol

1. IntialETHCrowdfund 
This contract plays with important role in Party protocol helps to create the new ETH party and create new party. This contract is called by proxy contract. It have responsible for raising ETH from contributors in exchange for party tokens.When a contributor sends ETH to the InitialETHCrowdfund contract, they receive an equivalent number of party tokens. If the crowdfund is successful, the party will use the ETH raised to fund its operations. If the crowdfund is unsuccessful, all ETH will be refunded to contributors.

Core functions are :-
`initialize()`
This function is responsible for the initialize the ETHCrowdFund , create new party and mint equivalent party tokens to the contributors. It takes four arguments `crowdfundOpts` , `partyOpts` , `customMetadataProvider` and `customMetadata`. Firstly it calls the `_createParty()` internal function in order to create the new party next it calls intialize function to create the ETHCrowdFund and finally calls the internal `_contribute()` function to mint equivalent party tokens to contributor.

`contribute()`
This function is used to collect ETH from contributor and give back equivalent party tokens. It takes three arguments `tokenId` , `delegate` and `gateData`. First it calls `_contribute()` internal function inside this have multi validation system and calls `_processContribution()` internal function to validate and finalize the contribution and updates the state variables with voting power.In this function `msg.sender` considered as contributor.

`batchContribute()`
This function do same process of `contribute()` but this function can be called for batch contribution. By taking the `BatchContributeArgs` struct as the arguments.Finally it refunds if the extra eth sends by contibutor.

`contributeFor()`
This function process is same as `contribute()` function but in this caller can specific the contibutor as `recipient`.

`refund()`
The process of this function is refund the ETH back to contributor by burning equivalent of party tokens.Can be called to refund for self or on another's behalf.

`batchRefund()`
This function process same as `refund()` function with batch transaction it saving gas, as it reduces the number of transactions that need to be sent to the blockchain.

2.ETHCrowdfundBase.sol
ETHCrowdfundBase contract plays intermideate process betwwen process the contribution , converting the voting power to amount  and finalize the contribution.

Core functionalities are :-

`_processContribution()`
This function is used to process the contribution by taking the three arguments `contributor` , `delegate` and `amount`. It has multi validation system to validate the arguments provided , finalize the contribution and finally convert the amount to voting power to be provide to contibutor.

`convertVotingPowerToContribution()`
This function is used to convert the `votingPower` to `amount` by calculates the amount of ETH that the user has contributed by dividing the user's voting power by the exchange rate. The exchange rate is the rate at which ETH is exchanged for voting power in the crowdfunding campaign.The convertVotingPowerToContribution() function is a useful function for users who want to know how much ETH they have contributed to a crowdfunding campaign then checks if the crowdfunding campaign has a funding split. A funding split is a percentage of the funds raised in the crowdfunding campaign that is allocated to a specific recipient. If the crowdfunding campaign has a funding split, the function reduces the user's contribution amount by the funding split percentage. . The function is also useful for crowdfunding campaign creators who want to know how much ETH they have raised from each user.

`finalize()`
This function is used to finalize the contribution to crowdFund first check if the crowdfunding campaign is already finalized.If the crowdfunding campaign is not already finalized, check if the caller is the host of the campaign if the caller is not the host of the campaign, check if the crowdfunding campaign has reached its minimum goal.If the crowdfunding campaign has not reached its minimum goal, revert the transaction . If the crowdfunding campaign has reached its minimum goal, or if the crowdfunding campaign has expired and been won, finalize the crowdfunding campaign.The finalize() function is a critical function in the ETHCrowdfundBase contract. It allows the host of a crowdfunding campaign to finalize the campaign and distribute the funds raised in the campaign. The function is also critical for contributors to crowdfunding campaigns, as it allows them to receive their voting power and claim their refunds if the campaign does not reach its goal.

`_finalize()`
This function is used to called by the above function `finalize()` function Deletes the expiry date of the crowdfunding campaign.
Transfers the funding split to the recipient, if applicable. A funding split is a percentage of the funds raised in the crowdfunding campaign that is allocated to a specific recipient.
Updates the party's total voting power. The party's total voting power is increased by an amount equal to the total amount of contributions to the campaign, multiplied by the exchange rate.
Transfers the ETH to the party.Emits a Finalized() event.

`sendFundingSplit()`
This function is sued to split the funding to the recipient if applicable.First checks the crowdfunding contribution is finalized if the funding spilt has been paid and checks the if the funding split has been paid. Sets the `fundingSplitPaid` flag to true , calculates the amount of the funding split and transfers the funding split to the recipient finally emits the FundingSplitSent() event.

`emergencyExecute()`
This function is used to allows the DAO to execute an arbitrary function call from the contract . It takes three arguments `targetAddress` , `targetCalldata` and `amountETH`. First it checks the caller is whether the DAO or not. Checks that emergency actions are not disabled and Calls the target function on the target contract with the specified data and ETH amount finally Emits an EmergencyExecute() event.

3. PartyGovernance.sol
This contract is abstract which the vital role in party DAO , the main functionalites are :-
Proposal creation and voting: Users can create proposals to change the Party DAO protocol or to take other actions on behalf of the Party DAO. Other users can then vote on these proposals.
Delegation: Users can delegate their voting power to other users. This allows users to give their vote to someone they trust to make decisions on their behalf.
Quorum and supermajority requirements: Proposals must meet a certain quorum (minimum percentage of voting power participating) and supermajority (minimum percentage of votes in favor) in order to pass.


`distribute()`
function in the PartyGovernance.sol contract creates a token distribution by moving the party's entire balance to the TokenDistributor contract and immediately creating a distribution governed by this party. By taking four arguments `amount` , `tokenType` , `token` and `tokenID` . Before creating a token distribution, the distribute() function performs several checks. First, it checks if the party is calling the function on itself. If so, the function skips the following checks. Otherwise, the function checks if distributions require a vote. If they do, the function reverts. The function then checks if the caller is an active member of the party and if the party has started. If either of these checks fails, the function reverts.Once the checks are complete, the function gets the address of the token distributor and emits a DistributionCreated() event. It then notifies third-party platforms that the governance NFT metadata has updated for all tokens by emitting a BatchMetadataUpdate() event. Finally, the function creates a token distribution using the token distributor.

`propose()`
This function is used to create on-chain actions in certain party DAO by taking the two arguments are `proposal` and `lastestSnapIndex` . First it checks whether the caller is active ember or not , assign the values to `ProposalStateValues` struct and calls the internal `accept()` function.

`accept()`
This function plays vital role in proposing the proposal with voting power cast mechanism. It takes the `proposalId` and `snapshotIndex` . First it gets the specific proposal state by proposal ID and validates through multiple if statements. and checks if member try to vote twice or not and gets the voting power of the member adds the caller's voting power to the total number of votes that have been cast on the proposal increment the number of hosts who have accepted the proposal and updates the proposal status if it has recahed the pass thereshold,emits a ProposalAccepted() event finally return the total number of votes that have been cast on the proposal.

`veto()`
This function is used to get reject the proposal. This means that the proposal will never be executable and cannot be voted on anymore. However, a proposal that has been already executed at least once (in the InProgress status) cannot be vetoed.By taking `proposalId` arguments .First asserts that the caller is a party host. Then, it sets the proposal's votes property to -1 to indicate a veto. Next, it checks the proposal's status and reverts if the proposal is not in one of the following states: Voting, Passed, or Ready. Finally, it emits a ProposalVetoed() event and notifies third-party platforms that the governance NFT metadata has updated for all tokens by emitting a BatchMetadataUpdate() event.

`execute()`
This function is used to excutes the proposal that has been passed governance. By taking `proposalId` , `proposal` , `preciousTokens` , `preciousTokenIds` , `progressData` and `extraData`. First it assert that not globally disabled and active menber validates the proposal hash.the proposal's status to ensure that it is executable or has already been executed but still has more steps to go. If the proposal has not been executed yet, the function makes sure that it has not expired. The function then sets the proposal's executedTime property to the current timestamp and checks that the precious list is valid. To avoid the proposal from being executed again in a deeper call, the function preemptively sets it to completed. Finally, the function executes the proposal and sets its completedTime property to 0 if the proposal did not complete.

`cancel()`
This function is used to cancel the proposal . First itasserts that the caller is an active member of the party and gets information about the proposal. It then validates the proposal hash to ensure that it has not changed since it was proposed and checks the proposal's status to make sure that it is in the InProgress state. To mitigate parties accidentally getting stuck forever by setting an unrealistic cancelDelay or being reckless with too low a cancelDelay, the function limits the cancelDelay to the global max and min cancel delay. Next, the function checks that the proposal cannot be cancelled yet. If all the above checks are passed, the function marks the proposal as cancelled by setting the completed time to the current time with the high bit set. It then calls the proposal execution engine to perform the cancel, emits a ProposalCancelled() event, and notifies third-party platforms that the governance NFT metadata has updated for all tokens by emitting a BatchMetadataUpdate() event.

`_adjustVotingPower()`
first gets the voter's last voting power snapshot and current delegate. If the voter has never delegated, the function sets the voter's delegate to themselves. If the new delegate is zero, the function uses the current (old) delegate. Next, the function creates a new voting power snapshot for the voter with the adjusted voting power and the new delegate. The function then inserts the new voting power snapshot into the voter's voting power snapshot history and sets the voter's delegate to the new delegate. Finally, the function emits a PartyDelegateUpdated() event, even if the delegate did not change, and rebalances the voter's delegates.

`rebalanceDelegates()`
This function is used to rebalancing the voter's delegates function first checks if the new delegate or old delegate is zero. If either of them is zero, the function reverts with an InvalidDelegateError. If the old delegate is not the voter and is not the new delegate, the function removes past voting power from the old delegate by getting the old delegate's last voting power snapshot, subtracting the voter's intrinsic voting power from their delegated voting power, and updating the old delegate's last voting power snapshot with the new delegated voting power. If the new delegate is not the voter, the function adds new voting power to the new delegate by getting the new delegate's last voting power snapshot, adding the voter's intrinsic voting power to their delegated voting power (subtracting the voter's old intrinsic voting power from the new delegate's delegated voting power if the new delegate is the same as the old delegate to avoid double counting), and updating the new delegate's last voting power snapshot with the new delegated voting power.

4.ProposalExecutionEngine.sol

This contract plays vital role in proposal execution, cancellation and validsignature and it plays vital role in partyDAO ecosystem .

Core functionalites are :-
`executeProposal()`
This fucntion which called by partyGovernance contract through proxy by `execute()` function checks if the proposal ID is valid and if there is no other proposal currently in progress. It then gets the next progress data hash from storage and compares it to the expected progress data hash. If the two hashes match, the function executes the proposal. If the progress data is empty, the function marks the proposal as complete.

`cancelProposal()`
This fucntion is used to cancel the proposalID which is called by the PartyGovernance contract by the `cancel()` external through delegate call. First checks if the proposal ID is valid. If it is not, the function reverts with a ZeroProposalIdError. The function then gets the current in-progress proposal ID from storage. This is necessary to ensure that only the current in-progress proposal can be cancelled. Next, the function checks if the current in-progress proposal ID is the same as the proposal ID of the current proposal. If it is not, the function reverts with a ProposalNotInProgressError. Finally, the function clears the current in-progress proposal ID and the next progress data hash. This marks the proposal as cancelled and prevents it from being resumed.

`ValidSignature()`
This function validates a signature by first getting the signature validator for the given hash. If the signature validator is set to address(1), the signature is always valid. Otherwise, the function calls the signature validator's isValidSignature() function to validate the signature. If the transaction origin is equal to address(0), the function gets and calls the signature validator for the hash 0, or the global off-chain signature validator if the signature validator for the hash 0 is equal to address(0).

5. PartyGovernanceNFT.sol

This contract act as the intermediates between party and party Governance by implementing the governance NFT functionality for the Party DAO. It allows users to mint governance NFTs, which represent their voting power in the Party DAO. Governance NFTs are also used to distribute rewards to Party DAO members. The vital process are :-
mintGovernanceNFT: This function allows users to mint a governance NFT. It takes the user's voting power as an argument and returns a governance NFT.
burnGovernanceNFT: This function allows users to burn a governance NFT. It takes the governance NFT ID as an argument and burns the governance NFT.
RageQuit : This function is used to burn a governance NFT and withdraw a fair sahre of fungible tokens from the party


The Core functionalities are :-
`mint()`
This function plays vital role in party governance mechanism first the caller must be an authority. The caller who wants to mint Governace NFT can then get the total voting power of the Party DAO and cap the voting power of the NFT to the remaining unminted voting power supply. Next, the caller updates the state of the Party DAO to reflect the new NFT. This includes incrementing the token count, setting the voting power of the NFT, and adjusting the voting power of the owner of the NFT. Finally, the caller mints the NFT to the user.

`IncreaseVotingPower()`
This is to used to increase the voting power first asserts that the caller is an authority. Then, it gets the total voting power of the Party DAO and the amount of voting power that has already been minted. If the total voting power is zero, the function allows the caller to mint more voting power than the remaining unminted voting power supply. Otherwise, the function caps the voting power to the remaining unminted voting power supply.Next, the function updates the state of the Party DAO to reflect the new NFT. This includes incrementing the token count, setting the voting power of the NFT, and adjusting the voting power of the owner of the NFT. Finally, the function emits an event to notify interested parties of the change.

`decreaseVotingPower()`
This function is used to decrease the voting power and this fucntion process is vise-vers of `increaseVotingPower()`.

`_burnAndUpdateVotingPower()`
This is used to burn the voting power and NFT and first it checks the caller must be authorized to burn the token. If the checkIfAuthorizedToBurn parameter is set to true, the function checks if the caller is the owner of the token, has been approved to burn the token, or is an approved operator for the owner of the token. If the caller is not authorized to burn the token, the function reverts.Once the caller's authorization has been verified, the function gets the voting power of the token. This is necessary to ensure that the total voting power of the Party DAO is updated correctly. The function then increments the total voting power burned by the amount of voting power of the token being burned.Next, the function deletes the voting power associated with the token being burned. It then adjusts the voting power of the owner of the token to account for the burning of the token. Finally, the function burns the token and emits a burn event to notify interested parties of the burn.

`rageQuit()`
This function is used to burn a governance NFT and withdraw a fair share of fungible tokens from the party. This plays the vital role in party DAO ecosystem and economy activites of the firm. First check if they have any governance NFTs to burn. If they do, they must then check if they are an authority. If they are not an authority, they must check if rage quit is allowed. Once they have confirmed that they can rage quit, they must use a reentrancy guard to prevent the function from being called recursively. They must then update the last rage quit timestamp and sum up the total amount of each token to withdraw. Next, they must burn their party cards and withdraw the tokens from the party and transfer them to the recipient. Finally, they must emit a RageQuit event to notify interested parties of the rage quit.

## 4. Centralization risks 

The parties and functionalities depend on single host authority all were landed in centralization risk. Functions are :-
[`abidicate()`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L457C1-L473C1) function.
[`veto()`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L665C1-L681C10) function.
[`setRageQuit()`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L318C1-L336C6) function.
The above functions will be landed in risky situation if party have single host and double host one of it compromised.

## 5. Mechanism review 

After manual testing, we concluded that a Party DAO is a decentralized autonomous organization (DAO) designed to manage and operate a party. Party DAOs are typically governed by a set of rules and regulations encoded in a smart contract. This smart contract defines the rights and responsibilities of all DAO members, as well as the rules for making decisions about the party.
Hosts have more authority than members. New users who want to become members can contribute to the existing party and receive an equivalent share of the party's voting power. After that, hosts or members can propose on-chain changes. If a majority of party members vote in favor of a proposal, it will be executed after the governance period has passed.The governance can mint and burn governance NFTs by minting/burning voting power.

**Comparison of Party DAO and Party Hats DAO**
| Particulars   | Party DAO  | Party Hats DAO  |
|---|---|---|
|Purpose   | Decentralized autonomous organization (DAO) designed to manage and operate a party  |Decentralized autonomous organization (DAO) designed to manage and operate a party rental company   |
|Governance   |Governed by a set of rules and regulations that are encoded in a smart contract   |Governed by a set of rules and regulations that are encoded in a smart contract   |
|Membership   |Members purchase governance tokens to gain membership and voting rights   |Members purchase governance tokens to gain membership and voting rights   |
|Voting   |Members can vote on proposals that affect the party   |Members can vote on proposals that affect the party rental company   |
|Decision-making   |Proposals that receive a majority vote are implemented   |Proposals that receive a majority vote are implemented   |
|Smart contracts   |Used to automate many of the tasks that are associated with party management   |Used to automate many of the tasks that are associated with running a party rental company   |
|Transparency   |All transactions and decisions are recorded on the blockchain   |All transactions and decisions are recorded on the blockchain   |
|Accountability   |DAO managers are accountable to the DAO's members   |	DAO managers are accountable to the DAO's members   |
|Challenges   |Complex and difficult to manage, vulnerable to attack by malicious actors   |Complex and difficult to manage, vulnerable to attack by malicious actors   |
|Benefits   |More transparent and democratic than traditional party management structures, more efficient, more secure   |More transparent and democratic than traditional party rental company management structures, more efficient, more secure   |
|Future   |Likely to become more widespread and sophisticated as blockchain technology continues to develop   |Likely to become more widespread and sophisticated as blockchain technology continues to develop   |

## 6 .Recommendation

During manual testing, we observed that the abdicateHost() function allows the caller to transfer the host authority to a specified address. However, we noticed that some on-chain parties only have a single host. If the host wallet of a party with a single host is compromised, the party and its members will be at risk of centralization. Additionally, in the case of a party with two hosts, if one of the hosts is compromised after the party is created, the normal host cannot transfer the host authority to another host because the abdicateHost() function only allows the caller to specify a single address. As a result, the compromised account will remain associated with the party for the duration of its life cycle. We recommend that the functionality be expanded to allow the transfer of host authority from one address to another. like below :-

```solidity
    function abdicateHost(address oldPartyHost ,address newPartyHost) external {
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
        isHost[oldPartyHost] = false;
        emit HostStatusTransferred(oldPartyHost, newPartyHost);
    }

``` 
The above function eliminates the centralization and risky situation for both party and it's members .

## 7. Hours spend 
64 hours



### Time spent:
64 hours