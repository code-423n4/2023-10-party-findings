### [N-01] Token and tokenId can be defined into a struct.
Token address and token id always appear together. It's better to define them together into a struct.
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
53:        // The tokens that are considered precious by the party.These are
54:        // protected assets and are subject to extra restrictions in proposals
55:        // vs other assets.
56:        IERC721[] preciousTokens;
57:        // The IDs associated with each token in `preciousTokens`.
58:        uint256[] preciousTokenIds;

File: contracts/party/PartyGovernance.sol
1138:        IERC721[] memory preciousTokens,
1139:        uint256[] memory preciousTokenIds

File: contracts/party/PartyGovernance.sol
1148:        IERC721[] memory preciousTokens,
1149:        uint256[] memory preciousTokenIds

File: contracts/party/PartyGovernance.sol
1155:        IERC721[] memory preciousTokens,
1156:        uint256[] memory preciousTokenIds

File: contracts/party/PartyGovernanceNFT.sol
87:        IERC721[] memory preciousTokens,
88:        uint256[] memory preciousTokenIds,
```
[[L53-L58]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L53-L58) | [[L1138-L1139]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1138-L1139) | [[L1148-L1149]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1148-L1149) | [[L1155-L1156]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1155-L1156) | [[L87-L88]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L87-L88)


### [N-02] Incomplete comment statements.
Comments are truncated accidently at L61 and L66:
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
61:        // Initial authorities to set on the party
62:        address[] authorities;

File: contracts/crowdfund/InitialETHCrowdfund.sol
66:        // IDs of cards to credit the contributions to. When set to 0, it means
67:        uint256[] tokenIds;
```
[[L61-L62]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L61-L62) | [[L66-L67]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L66-L67)

### [N-03] Consider implementing ```batchContributeFor``` similar to ```batchContribute```.
```batchContributeFor``` and ```batchContribute``` have similar logic, it's better to implement them similarly for consistency and maintainability. Specifically, using ```ethAvailable``` instead of ```valueSum```, and refund excess ```ethAvailable```.
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol
258:        votingPowers = new uint96[](args.recipients.length);
259:        uint256 valuesSum;
260:        for (uint256 i; i < args.recipients.length; ++i) {
261:            votingPowers[i] = _contribute(
262:                args.recipients[i],
263:                args.initialDelegates[i],
264:                args.values[i],
265:                args.tokenIds[i],
266:                args.gateDatas[i]
267:            );
268:            valuesSum += args.values[i];
269:        }
270:        if (msg.value != valuesSum) { // @audit: [N] process like `batchContribute`
271:            revert InvalidMessageValue();
272:        }
```
[[L258-L272]](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L258-L272)
