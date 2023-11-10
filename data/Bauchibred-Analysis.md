# Analysis

## Table of Contents

- [Approach](#approach)
- [Architecture Overview](#architecture-overview)
- [Testability](#testability)
- [Centralization Risks](#centralization-risks)
- [Systemic Risks](#systemic-risks)
- [Summary of Reported H/M findings](#summary-of-reported-hm-findings)
- [Summary of Reported QA findings](#summary-of-reported-qa-findings)
- [Learnt About](#learnt-about)
- [Other Recommendations](#other-recommendations)
- [Security Researcher Logistics](#security-researcher-logistics)
- [Conclusion](#conclusion)

## Approach

The auditing process began even before the contest started, grasping a high level but brief overview of the entire Party DAO ecosystem, i.e glancing through both [the docs](https://docs.partydao.org) & the [official repo of the Party Protocol](https://github.com/PartyDAO/party-protocol).

Once the contest began, the first day was dedicated to examining the smart contracts in scope, after this review, approximately 6 hours spread was spent on previous audit contests reports that were publicly accessible _(links to reports are attached in the [Security Researcher Logistics](#security-researcher-logistics) section)_ this was done in order to examine findings, drawinsights from them & see if the recommended fix really correctly mitigates the issue or if the fix was never correctly deployed.

This was then followed by a meticulous, line-by-line security review of the sLOC in scope, beginning from the contracts small in size like `OffChainSignatureValidator.sol` ascending to the bigger contracts like the`PartyGovernance.sol`. The concluding phase of this process entailed interactive sessions with the developers on Discord. This fostered an understanding of unique implementations and facilitated discussions about potential vulnerabilities and observations that I deemed significant, requiring validation from the team.

## Architecture Overview

- 1. **InitialETHCrowdfund.sol (300 SLOC)**  
     The `InitialETHCrowdfund.sol` contract as understood is the gateway for initiating a new Ethereum party. This contract oversees the crowdfunding process, enabling a collective of participants to pool their ETH to form a new party.

- 2. **ETHCrowdfundBase.sol (251 SLOC)**  
     `ETHCrowdfundBase.sol` establishes the core functionalities for the ETH crowdfund mechanism. This contract is the base for `InitialETHCrowdfund.sol` and encapsulates the common logic that would be utilized by various types of crowdfunding activities within the ecosystem.

- 3. **PartyGovernance.sol (778 SLOC)**  
     `PartyGovernance.sol` is a comprehensive contract that imbues the `Party` contract with its governance capabilities. And is the most substantial contract in terms of source lines of code for this contest, i execute proposals, and interact with the broader protocol. It includes the core governance logic and is inherited by the `Party` contract, note that this contract is to comply with two different EIPs: `EIP-4906`, `EIP-165`.

- 4. **PartyGovernanceNFT.sol (313 SLOC)**  
     As previously hinted, the`PartyGovernanceNFT.sol` contract is an extension of the `Party` contract that incorporates NFT (non-fungible token) functionalities into the party's governance structure. It inherits NFT from `solmate/ERC721` and adhering to royalty standards as specified by `openzeppelin/contracts/interfaces/IERC2981.sol`, this contract is also set up to comply with with three different EIPs: `EIP-721`, `EIP-165` &`EIP-2981`.

- 5. **ProposalExecutionEngine.sol (244 SLOC)**  
     The `ProposalExecutionEngine.sol` serves as a dynamic executor of proposals within the party's ecosystem. It is designed to be delegate called from parties, parties use a fallback to perform a static delegate call to this contract, possibly for read-only proposal-related operations. This contract is also set up to comply with with three different EIPs: `ERC1271`.

- 6. **ProposalStorage.sol (47 SLOC)**  
     The `ProposalStorage.sol` contract acts as a shared storage module for other contracts in the system. With a relatively small codebase, it's focused on providing a consistent and secure storage pattern that other contracts can access and manipulate due to it's implementation of the **bucket-like storage system.**

- 7. **SetGovernanceParameterProposal.sol (55 SLOC)**  
     `SetGovernanceParameterProposal.sol` is just a new proposal type specifically geared towards the modification of governance parameters within a party.

- 8. **SetSignatureValidatorProposal.sol (40 SLOC)**  
     The `SetSignatureValidatorProposal.sol` contract introduces a proposal type that enables the setting or validation of signature validators within the ecosystem. Great initiative cause this allows parties to establish trust with external entities or validate certain operations through signature validation.

- 9. **OffChainSignatureValidator.sol (60 SLOC)**  
     `OffChainSignatureValidator.sol` is a specialized contract dedicated to verifying off-chain signatures. It reconstitutes messages to ensure they originate from a trusted source and confirms their validity if the signer possesses enough voting power or is an acknowledged member of the party. This contract is also set up to comply with with `EIP-1271`.

- 10. **Implementation.sol (32 SLOC)**  
      `Implementation.sol` is a utility contract that provides essential helper functions for implementation contracts used from a proxy.

### Important Links

- **Previous Audits:** [2023-05-party](https://github.com/code-423n4/2023-05-party), [2023-04-party](https://github.com/code-423n4/2023-04-party), [2022-09-party](https://github.com/code-423n4/2022-09-party)
- **Documentation:** [https://docs.partydao.org](https://docs.partydao.org/)
- **Website:** https://www.party.app/
- **Twitter:** [https://twitter.com/prtyDAO](https://twitter.com/prtyDAO?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- **Discord:** https://discord.gg/pMPcwMDtyR

For a deeper dive into the docs, I would recommend going through the following sections of the docs:

- [Introduction](https://docs.partydao.org/docs/Introduction)
- [Party: Overview](https://docs.partydao.org/docs/partys/Overview)
- [Crowdfund: Overview](https://docs.partydao.org/docs/crowdfund/Overview)
- [Party](https://docs.partydao.org/docs/partys/Party)

## Testability

The system's testability is pretty much average, _if not below average_, current coverage is just 62% which in my honest opinion is way less that what's expected for a project of this magnitude, another thing to note would be the occasionally intricate lengthy tests that are a bit hard to follow, this shouldn't necessarily be considered a flaw though, cause once familiarized with the setup, devising tests and creating PoCs becomes a smoother endeavour. In that sense I'd commend the team for having provided a high-quality testing sandbox for implementing, testing, and expanding ideas.

## Centralization Risks

- Rage quitting is only allowed before rageQuitTimestamp or if permanently enabled, but note that, the host can change rageQuitTimestamp at will. This enables the host to frontrun a rage quitter with a resetting of rageQuitTimestamp, reverting his rage quit. Therefore a user can never be certain that he can rage quit. The user might have entered the party with the expectation, or even on condition, that he has the choice to rage quit (for the time being). There should be some guarantee that this functionality is available when it says it is.

- The ETHCrowdfundBase's "emergencyExecute" function, designed for DAO-controlled emergency actions, is vulnerable to host preemption due to the `disableEmergencyExecute` feature. Hosts currently possess the ability to immediately emergency actions, potentially undermining the DAO's responsive capacity in critical situations. Since _malicious hosts_ can just listen to the mempool and front run calls to `emergencyExecute()` with ``disableEmergencyExecute()`, an idea of mitigating this would be to use a timelock.

## Systemic Risks

- The cancelProposal() can put a party's system in a completely broken state and should be done carefully
- The constructor of `PartyGovernanceNFT` is marked as `payable` this could be a decision to save gas by avoiding the check, but it is important to note that the savings would be extremely marginal over the risk of potentially sending ETH to the implementation contract since this sits behind a proxy.

- The `_executeUpgradeProposalsImplementation()` is a bit risky since it always upgrade to latest implementation stored in `_GLOBALS`, now if new implementation is a bi faulty admin must change to it and can't provide a different implementation to pass the call to.

- The `_rageQuit()` feature seems to be a bit flawed, this is cause someone with a large enough holding calling `rageQuit()` could potentially decide the outcome of votes, and this could impact more that the votes of the caller, cause just as human nature a user that might have voted could see that enough votes is present and decide on not voting just for someone else to call `rageQuit()` at the late stages of the voting process, effectively having the proposal's decision not representing the majority's will.

## Summary of Reported H/M findings

During my security review, I identified several medium-severity findings impacting the smart contract functionalities, each presenting unique challenges and potential risks to the system's overall integrity and compliance.

A loophole was identified in the contribution process, particularly with the `contributeFor()` function. This allows users who are not permitted by gatekeepers to still partake in contributions through a third party. The root of the issue lies in the gatekeeper check that focuses on the `msg.sender` instead of the actual contributor. To rectify this, the recommendation is to modify the logic to ensure that the actual contributor is always verified against the gatekeeper's allowlist, thereby maintaining the integrity of private crowdfunds and adhering to legal compliance.

Another significant issue is observed in the crowdfund finalization process. The current setup could lead to crowdfunds being stuck in an 'active' state and eventually being classified as 'lost' due to a deadlock in achieving `maxTotalContributions`. This situation occurs when `minTotalContributions` is set equal to `maxTotalContributions`, and the remaining contribution required is less than the `minContribution`. The proposed solution involves adjusting the initialization logic to ensure that `minTotalContributions` **is never equal to** greater than `maxTotalContributions`.

Two notable observations were in regards to the compliance of some contracts to certain EIPs, namingly the non-compliance of `PartyGovernanceNFT.sol` with EIP-2981. The contract's `royaltyInfo` function fails to adhere to the EIP's stipulation that royalty payments should be calculated based on the sale price, instead returning a fixed royalty amount. Similarly, the `PartyGovernance.sol` contract's adherence to EIP-4906 is also flawed, given the incorrect implementation of the `MetadataUpdate` and `BatchMetadataUpdate` events. These issues not only breach standard protocols but also potentially mislead third-party platforms relying on accurate metadata updates.

An interesting one relates to the potential difficulty in achieving unanimous votes for certain proposals. This arises from a precision loss issue in the `convertVotingPowerToContribution()` function leading to a discrepancy between the calculated and actual voting power. Similar to a previous finding. The problem is exacerbated by the fact that it could hinder the special features that rely on the unanimous consensus.

Another one is in regards to the `cancelProposal()` function, where canceling a proposal inadvertently resets the `current InProgress proposal ID and next progress data` to zero. This action disrupts the proposal execution flow in `executeProposal()`, as each proposal's execution is somewhat chained. After a proposal is canceled, any subsequent attempts to execute another proposal would fail. This is because the reset `nextProgressDataHash` and `currentInProgressProposalId` values conflict with the expected state for executing new proposals, leading to either assertion failure or invalid proposal progress data errors.

In `ETHCrowdfundBase.sol`, a contextual flaw is present in the crowdfunding process, particularly when funding splits are activated. The current logic deducts a funding split from total contributions, leading to scenarios where a crowdfund's lifecycle is finalized without the party receiving the full intended funding. This is especially problematic when `minTotalContributions` is set equal to or near `maxTotalContributions`, leaving the party underfunded and potentially jeopardizing planned activities or objectives.

Lastly, the `accept()` function in `PartyGovernance.sol` seems to exhibits a significant vulnerability to multi-block and uncle block attacks. The current safeguard against the exploit noted in the codebase, which checks the `lastRageQuitTimestamp` against the `block.timestamp`, does not account for the possibility of such sophisticated attack vectors. This oversight could enable attackers to exploit the voting process, particularly in manipulating the outcomes of proposals, thereby undermining the democratic integrity of the governance mechanism.

## Summary of Reported QA Findings

The first one I'd like to note evolves around the `emergencyExecute()` function. Its effectiveness is under scrutiny due to the possibility of the host overriding the DAO's decisions in crucial situations. This undermines the purpose of the function, which is intended for emergency actions by the DAO. A proposed solution involves introducing a time lock on the disabling function, ensuring the host cannot immediately obstruct DAO's emergency executions.

Another significant issue is the potential misuse of the `_rageQuit()` function, which could disproportionately influence the outcomes of proposals. Members holding substantial voting power might sway the voting process and then abruptly withdraw, thus compromising the integrity of decision-making. A time buffer to restrict `rageQuit()` execution towards the end of the voting process could effectively mitigate this risk.

A separate observation pertains to the redundancy in the naming convention of custom errors within the `PartyGovernanceNFT` smart contract. It is suggested that the superfluous "Error" suffix in custom error names be removed to enhance clarity and align with standard Solidity conventions.

There's also a concern regarding the execution window of the `rageQuit()` function. The current logic allows execution at the precise `block.timestamp` marking the deadline, potentially leading to governance issues. Adjusting the conditional check to include this deadline moment could address this inaccuracy.

The compatibility of contracts compiled with Solidity `0.8.20’ on BASE is questioned due to the lack of support for certain opcodes, such as PUSH0. It is recommended to follow BASE's guideline of using Solidity `0.8.19’ or earlier versions until BASE updates its opcode support.

An inconsistency is identified in the implementation of `batchContribute’ and `batchContributeFor’. The latter's lack of a mechanism to refund excess ETH, unlike the former, creates an operational inconsistency. Harmonizing `batchContributeFor’ with `batchContribute’ to include logic for handling and refunding excess ETH is advised.

A typographical error in the variable name `signingThersholdBps’ is highlighted, with a correction to `signingThresholdBps’ recommended for improved clarity and consistency.

The `burn()` function's vulnerability to reversion due to duplicate `tokenIds’ in its input array is a technical oversight. Introducing a check to prevent processing of duplicate `tokenIds’, akin to the logic in `rageQuit()’, is suggested.

The self-delegation mechanism within the context of crowdfund contributions is flawed. This loophole allows an attacker to redirect the voting power of NFTs minted post-crowdfund to themselves, undermining the governance structure. Adjusting the contribution logic to automatically set self-delegation when contributing with `delegate’ as `address(0)’ is proposed.

Lastly, there are contextual errors in the documentation, specifically in the description of the `contribute’ function, which inaccurately mirrors that of `contributeFor()’. Revising the documentation to accurately reflect the functionality of `contribute’ is recommended to ensure clarity and proper understanding.

## Learnt About

During the course of my security review, I had to research some stuffs to understand the context in which they are currently being used, some of which led to me learning new things and having refreshers on already acquainted topics. Some of these topics are listed below:

- Explicit storage buckets.
- `EIP-4906`.
- `EIP-2981`.
- Had a refresher on the `EIP-165` & `EIP-1271`.

## Other Recommendations

### **Enhance Documentation Quality**

I'd like to start by commending the quality of the [docs](https://docs.partydao.org/). They are generally well-crafted. However, there are some gaps concerning what's within the scope. It's important to remember that code comments also count as documentation, and there have been some inconsistencies in this area. Comprehensive documentation not only provides clarity but also facilitates the onboarding process for new developers. For instance, the `PartyGovernanceNFT` smart contract unnecessarily appends the term "Error" to its custom error names, such as `FixedRageQuitTimestampError`, `CannotRageQuitError`, and others, which is redundant since Solidity's reverts clearly indicate an error condition. This redundancy does not affect the contract's functionality or security but impacts code readability and brevity. It is recommended to remove the "Error" suffix to align with common Solidity practices, thereby improving code clarity and maintainability without the extraneous term. For example, `FixedRageQuitTimestampError` should be simplified to `FixedRageQuitTimestamp`, making the code cleaner and more in line with widely accepted naming conventions.

### **Onboard More Developers**

Having multiple eyes scrutinizing a protocol can be invaluable. More contributors can significantly reduce potential risks and oversights. Evidence of where this could come in handy can be gleaned from codebase typos, e.g how `thershold` is used instead of `threshold` in multiple instances even in mappings, another reason why is the availability of the developers during the auditing process. i.e how hard it was to get a hold of developers on discord to talk about potential bug cases. Other cases are simple contextual errors from the docs. I could keep going on and on, but the main aim of this is to hint that having more people in the team would really help in a large margin to resolve this.

### **Leverage Additional Auditing Tools**

Many security experts prefer using Visual Studio Code augmented with specific plugins. While the popular [Solidity Visual Developer](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor) has already been integrated with the protocol, there's room for incorporating other beneficial tools.

### **Enhance Event Monitoring**

Current implementation subtly suggests that event tracking isn't maximized. Instances where events are missing or seem arbitrarily embedded have been observed. A shift towards a more purposeful utilization of events is recommended.

### **Improve Testability**

As already stated in this report in regards to the current level of testing _there's a very bug room for further refinement and improvement._ Main goal is to have +95% code coverage.

### **Refine Naming Conventions**

There's a need to improve the naming conventions for contracts, functions, and variables. In several instances, the names don't resonate with their respective functionalities, leading to potential confusion, in other cases clear typos have been made in the names like using `thershold` instead of `threshold`, another one to note would be the redundant suffixing of `Error` to error names, solidity's revert statements already indicate that an error condition has been met, making the additional "Error" suffix unnecessary.

## Security Researcher Logistics

My attempt on reviewing the Party Protocol spanned around ~ 35 - 40 hours distributed over 5 days:

- 1.5 hours dedicated to writing this analysis.

- 6 hours exploring all previous contests on Code4rena, viewable from the links below:

  - [2023-05 Party Protocol's Code4rena report](https://github.com/code-423n4/2023-05-Party-findings).
  - [2023-04 Party Protocol's Code4rena report](https://github.com/code-423n4/2023-04-Party-findings).
  - [2022-09 Party Protocol's Code4rena report](https://github.com/code-423n4/2022-09-Party-findings).

This was done to see if issues were correctly mitigated and if other instances of previous bugs exist in the current codebase

- 2 hours were allocated for discussions with sponsors on the private discord group regarding potential vulnerabilities.

- 2 hours over the course of the 4 days _(~30 minutes per day)_ was spent lurking in the public discord group to get more ideas based on explanation provided by sponsors to questions other security researchers have asked.

- The remaining time was spent on finding issues and writing the report for each of them on the spot, later on _editing a few with more knowledge gained on the protocol as a whole, or downgrading them to QA reports._

## Conclusion

The codebase was a very great learning experience, though it was a pretty hard nut to crack, seeing that it's like an update contest on relatively the same codebase and most _easy to catch_ bugs have already been spotted and mitigated from the previous contests/audits, This is not surprising, considering there have been three distinct reports on this from Code4rena, and multiple others from independent security researchers and even other auditing firms.


### Time spent:
35 hours