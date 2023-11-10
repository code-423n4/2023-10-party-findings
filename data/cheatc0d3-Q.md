L1 - In `initialize`, input validation can be improved for the parameters in `crowdfundOpts` and `partyOpts`.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L108

    **poc**:
    ```solidity
    function initialize() external payable onlyInitialize {
        require(crowdfundOpts.minContribution > 0, "Min contribution must be greater than 0");
        require(crowdfundOpts.maxContribution >= crowdfundOpts.minContribution, "Max contribution must be greater than or equal to min contribution");
        require(crowdfundOpts.duration > 0, "Duration must be greater than 0");
        
    }
    ```

L2 - In `contribute`, ensure `delegate` is not a zero address.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L164

    **poc**:
    ```solidity
    function contribute() public payable onlyDelegateCall returns () {
        require(delegate != address(0), "Delegate address cannot be zero");
        
    }
    ```
    
L3 - In `batchContributeFor`, it's necessary to validate that all passed arrays (`recipients`, `initialDelegates`, `values`, `tokenIds`, `gateDatas`) are of equal length to prevent logical inconsistencies.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L255

    **poc**:
    ```solidity
    function batchContributeFor() external payable onlyDelegateCall returns () {
        require(args.recipients.length == args.initialDelegates.length, "Array lengths must match");
        require(args.values.length == args.tokenIds.length, "Array lengths must match");
        require(args.gateDatas.length == args.tokenIds.length, "Array lengths must match");
     
    }
    ```

L4 - In `contributeFor`, ensure `recipient` and `initialDelegate` are not zero addresses.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L235

    **poc**:
    ```solidity
    function contributeFor() external payable onlyDelegateCall returns () {
        require(recipient != address(0), "Recipient address cannot be zero");
        require(initialDelegate != address(0), "Initial delegate address cannot be zero");
        
    }
    ```

L5 - In `batchContribute`, validate that the sum of `args.values` does not exceed `msg.value`.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L255

    **poc**:
    ```solidity
    function batchContribute() external payable onlyDelegateCall returns () {
        uint256 totalValue = 0;
        for (uint256 i = 0; i < numContributions; ++i) {
            totalValue += args.values[i];
        }
        require(totalValue <= msg.value, "Insufficient ETH sent");
       
    }
    ```
    
L6 - In `_createParty`, there should be a check to ensure that the `opts.preciousTokens` and `opts.preciousTokenIds` arrays are of equal length to ensure consistency.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L372

    **poc**:
    ```solidity
    function _createParty() private returns (Party) {
        require(opts.preciousTokens.length == opts.preciousTokenIds.length, "Mismatched precious tokens and token IDs");
        
    }
    ```

L7 - The function could include a check to ensure that none of the addresses in `opts.authorities` and `opts.governanceOpts.hosts` are zero addresses.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol#L372

    **poc**:
    ```solidity
    function _createParty() private returns (Party) {
        for (uint i = 0; i < opts.authorities.length; ++i) {
            require(opts.authorities[i] != address(0), "Authority address cannot be zero");
        }
        for (uint i = 0; i < opts.governanceOpts.hosts.length; ++i) {
            require(opts.governanceOpts.hosts[i] != address(0), "Host address cannot be zero");
        }
        
    }
    ```


L8 - In `onlyPartyDao` and `onlyPartyDaoOrHost`, a check to ensure that the `partyDao` address is not zero could be added for enhanced safety.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L231

    **poc**:
    ```solidity
    modifier onlyPartyDao() {
        address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
        require(partyDao != address(0), "DAO address is zero");
        if (msg.sender != partyDao) {
            revert NotAuthorized();
        }
        
    }

    modifier onlyPartyDaoOrHost() {
        address partyDao = _GLOBALS.getAddress(LibGlobals.GLOBAL_DAO_WALLET);
        require(partyDao != address(0), "DAO address is zero");
        if (msg.sender != partyDao && !isHost[msg.sender]) {
            revert NotAuthorized();
        }
        
    }
    ```

L9 - In the constructor, a check to ensure the `globals` parameter is not a zero address should be added.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L265

    **poc**:
    ```solidity
    constructor(IGlobals globals) {
        require(address(globals) != address(0), "Globals address cannot be zero");
        _GLOBALS = globals;
    }
    ```

L10 - In `_initialize`, add checks to ensure the lengths of `preciousTokens` and `preciousTokenIds` are equal.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L270

    **poc**:
    ```solidity
    function _initialize() internal virtual {
        
        require(preciousTokens.length == preciousTokenIds.length, "Array lengths must match");
        _setPreciousList(preciousTokens, preciousTokenIds);
        
    }
    ```

L11 - In `getVotingPowerAt`, add a check to ensure the `voter` address is not zero.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L355

    **poc**:
    ```solidity
    function getVotingPowerAt(address voter, uint40 timestamp) external view returns (uint96 votingPower) {
        require(voter != address(0), "Voter address is zero");
        
    }

    function getVotingPowerAt(address voter, uint40 timestamp, uint256 snapIndex) public view returns (uint96 votingPower) {
        require(voter != address(0), "Voter address is zero");
        
    }
    ```

L12 - In `delegateVotingPower`, validate that `delegate` is not a zero address.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L451

    **poc**:
    ```solidity
    function delegateVotingPower(address delegate) external {
        require(delegate != address(0), "Delegate address cannot be zero");
        
    }

    function abdicateHost(address newPartyHost) external {
        require(newPartyHost != address(0), "New host address cannot be zero");
        
    }
    ```

L13 - In `findVotingPowerSnapshotIndex`, add a check to ensure the `voter` address is not zero.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L423

    **poc**:
    ```solidity
    function findVotingPowerSnapshotIndex(address voter, uint40 timestamp) public view returns (uint256 index) {
        require(voter != address(0), "Voter address cannot be zero");
        
    }
    ```
    
L14 - In `distribute`, validate input parameters like `amount`, `tokenType`, `token`, and `tokenId` to ensure they meet expected criteria.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L487

    **poc**:
    ```solidity
    function distribute(
        uint256 amount,
        ITokenDistributor.TokenType tokenType,
        address token,
        uint256 tokenId
    ) external returns (ITokenDistributor.DistributionInfo memory distInfo) {
        require(amount > 0, "Amount must be greater than 0");
        require(token != address(0), "Token address cannot be zero");
        // Additional checks for tokenType and tokenId as needed
        
    }
    ```

L15 - In `propose`, validate the `proposal` object's fields to ensure they meet expected criteria.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L553

**poc**:
    ```solidity
    function propose(Proposal memory proposal, uint256 latestSnapIndex) external returns (uint256 proposalId) {
        // Validate proposal fields as necessary
        
    }

    ```
L16  - In `accept`, validate `proposalId` and `snapIndex` to ensure they are valid and meaningful.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L595

    **poc**:
    ```solidity
    function accept(uint256 proposalId, uint256 snapIndex) public returns (uint256 totalVotes) {
        require(_proposalStateByProposalId[proposalId].values.proposedTime != 0, "Invalid proposal ID");
        require(snapIndex != type(uint256).max, "Invalid snapshot index");
        
    }
    ```

L17 - In `execute`, validate input parameters like `proposalId`, `proposal`, `preciousTokens`, `preciousTokenIds`, `progressData`, and `extraData`.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L706

    **poc**:
    ```solidity
    function execute() external payable {
        require(proposalId != 0, "Invalid proposal ID");
        // Add validations for proposal fields as necessary
        require(preciousTokens.length == preciousTokenIds.length, "Array lengths must match");
        // Validations for progressData and extraData as necessary
        
    }
    ```

L18  - The `_getVotingPowerSnapshotAt` function performs input validation by checking the `hintIndex` within bounds and appropriate snapshot timestamps.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L904

    **poc**:
    ```solidity
    function _getVotingPowerSnapshotAt() internal view returns () {
        require(snapsLength != 0, "No snapshots available");
        require(hintIndex < snapsLength, "Hint index out of bounds");
        
    }
    ```

L19 - In `_transferVotingPower`, add validation to ensure neither `from` nor `to` is the zero address.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L937

    **poc**:
    ```solidity
    function _transferVotingPower(address from, address to, uint256 power) internal {
        require(from != address(0), "From address cannot be zero");
        require(to != address(0), "To address cannot be zero");
        
    }
    ```
    
L20 - In `_rebalanceDelegates`, there is a check to ensure neither the old nor the new delegate is a zero address. 

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L972

    **poc**:
    ```solidity
    function _rebalanceDelegates() private {
        require(newDelegate != address(0), "New delegate address cannot be zero");
        require(oldDelegate != address(0), "Old delegate address cannot be zero");
        
    }
    ```

L21 - The `_insertVotingPowerSnapshot` function checks whether the last snapshot has the same timestamp as the new one to decide whether to overwrite it or append a new entry.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1021

    **poc**:
    ```solidity
    function _insertVotingPowerSnapshot() private {
        require(voter != address(0), "Voter address cannot be zero");
        
    }
    ```

L22 - In `_rebalanceDelegates`, validate inputs like `voter`, `oldDelegate`, `newDelegate`, `oldSnap`, and `newSnap`.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L972

    **poc**:
    ```solidity
    function _rebalanceDelegates() private {
        require(voter != address(0), "Voter address cannot be zero");
        
    }
    ```

L23  - The function `_getLastVotingPowerSnapshotForVoter` can benefit from a check to ensure the `voter` address is not zero.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1043

    **poc**:
    ```solidity
    function _getLastVotingPowerSnapshotForVoter(address voter) private view returns () {
        require(voter != address(0), "Voter address cannot be zero");
        
    }
    ```

L24 - In `_setPreciousList`, there is a check to ensure that `preciousTokens` and `preciousTokenIds` arrays are of equal length.

https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol#L1137

    **poc**:
    ```solidity
    function _setPreciousList() private {
        require(preciousTokens.length == preciousTokenIds.length, "Mismatched array lengths");
        
    }
    ```

L25 - The functions `_isUnanimousVotes`, `_hostsAccepted`, `_areVotesPassing`, and `_hashPreciousList` could benefit from validating their input parameters for correctness.

    **poc**:
    ```solidity
    function _isUnanimousVotes() private pure returns (bool) {
        require(totalVotingPower > 0, "Total voting power must be greater than 0");
        
    }
    ```

L26 - In `initialize`, input validation can be improved for the parameters in `crowdfundOpts` and `partyOpts`.

    **poc**:
    ```solidity
    function initialize() external payable onlyInitialize {
        require(crowdfundOpts.minContribution > 0, "Min contribution must be greater than 0");
        require(crowdfundOpts.maxContribution >= crowdfundOpts.minContribution, "Max contribution must be greater than or equal to min contribution");
        require(crowdfundOpts.duration > 0, "Duration must be greater than 0");
        
    }
    ```

L27  - In `contribute`, ensure `delegate` is not a zero address.

    **poc**:
    ```solidity
    function contribute() public payable onlyDelegateCall returns () {
        require(delegate != address(0), "Delegate address cannot be zero");
        
    }
    ```

L28 - In `batchContributeFor`, it's necessary to validate that all passed arrays (`recipients`, `initialDelegates`, `values`, `tokenIds`, `gateDatas`) are of equal length to prevent logical inconsistencies.

    **poc**:
    ```solidity
    function batchContributeFor() external payable onlyDelegateCall returns () {
        require(args.recipients.length == args.initialDelegates.length, "Array lengths must match");
        require(args.values.length == args.tokenIds.length, "Array lengths must match");
        require(args.gateDatas.length == args.tokenIds.length, "Array lengths must match");
       
    }
    ```

L29  - In `contributeFor`, ensure `recipient` and `initialDelegate` are not zero addresses.

    **poc**:
    ```solidity
    function contributeFor() external payable onlyDelegateCall returns () {
        require(recipient != address(0), "Recipient address cannot be zero");
        require(initialDelegate != address(0), "Initial delegate address cannot be zero");
        
    }
    ```

L30 - In `batchContribute`, validate that the sum of `args.values` does not exceed `msg.value`.

    **poc**:
    ```solidity
    function batchContribute() external payable onlyDelegateCall returns () {
        uint256 totalValue = 0;
        for (uint256 i = 0; i < numContributions; ++i) {
            totalValue += args.values[i];
        }
        require(totalValue <= msg.value, "Insufficient ETH sent");
        
    }
    ```


L31 - In the constructor, a check to ensure that the `globals` parameter is not a zero address could be added.

    **poc**:
    ```solidity
    constructor(IGlobals globals) payable PartyGovernance(globals) ERC721("", "") {
        require(address(globals) != address(0), "Globals address is zero");
        _GLOBALS = globals;
    }
    ```

L32  - In the `_initialize` function, add checks to ensure that the lengths of `preciousTokens` and `preciousTokenIds` arrays are equal.


    **poc**:
    ```solidity
    function _initialize() internal {
        require(preciousTokens.length == preciousTokenIds.length, "Array lengths mismatch");
        
    }
    ```

L33 - For the functions `getDistributionShareOf` and `getVotingPowerShareOf`, input validation for `tokenId` can be added to check if it's a valid token.

    **poc**:
    ```solidity
    function getDistributionShareOf(uint256 tokenId) external view returns (uint256) {
        require(_exists(tokenId), "Token does not exist");
        
    }
    function getVotingPowerShareOf(uint256 tokenId) public view returns (uint256) {
        require(_exists(tokenId), "Token does not exist");
        
    }
    ```

L35 - In `royaltyInfo`, a zero address check can be implemented for the returned address.

    **poc**:
    ```solidity
    function royaltyInfo(uint256 tokenId, uint256 salePrice) external view returns (address receiver, uint256 royaltyAmount) {
        
        require(receiver != address(0), "Receiver is zero address");
        
    }
    ```

L36 - `tokenURI`, `contractURI`, and `royaltyInfo` could include checks to ensure the delegate call to the renderer is successful.

    **poc**:
    ```solidity
    function tokenURI(uint256 tokenId) public view override returns (string memory) {
        require(_delegateToRenderer(tokenId), "Delegate call failed");
        
    }
    ```

L37 - In `increaseVotingPower`, validate the `tokenId` to ensure it exists.
```solidity
function increaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        
    }
    ```
    
    **poc**
    ```solidity
    function increaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        require(_exists(tokenId), "Token ID does not exist");
        
    }
    ```


L38 - In `mint`, add checks for non-zero `owner` and valid `votingPower`.

    **poc**:
    ```solidity
    function mint(address owner, uint256 votingPower, address delegate) external returns (uint256 tokenId) {
        require(owner != address(0), "Owner cannot be zero address");
        require(votingPower > 0, "Voting power must be positive");
        
    }
    ```

L39 - In `decreaseVotingPower`, `increaseTotalVotingPower`, and `decreaseTotalVotingPower`, input validation for `votingPower` can be added to ensure it's within a valid range.
   - In `burn`, add validation for the `tokenIds` array to ensure it's not empty.


    ```solidity
    function decreaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        require(votingPower > 0, "Voting power must be positive");
        
    }
    function increaseTotalVotingPower(uint96 votingPower) external {
        require(votingPower > 0, "Voting power must be positive");
        
    }
    function decreaseTotalVotingPower(uint96 votingPower) external {
        require(votingPower > 0, "Voting power must be positive");
        
    }
    function burn(uint256[] memory tokenIds) public {
        require(tokenIds.length > 0, "No token IDs provided");
        
    }
    ```
    
L40 - `decreaseVotingPower` should check if the token ID exists before attempting to decrease its voting power.

**poc**:
    ```solidity
    function decreaseVotingPower(uint256 tokenId, uint96 votingPower) external {
        require(_exists(tokenId), "Token ID does not exist");
        
    }
    ```
L41 - `burn` should validate each `tokenId` in the loop to ensure it exists before attempting to burn it.


    **poc**:
    ```solidity
    function burn(uint256[] memory tokenIds) public {
        
        for (uint256 i; i < tokenIds.length; ++i) {
            require(_exists(tokenIds[i]), "Token ID does not exist");
            
        }
        
    }
    ```

L42 - The `rageQuit` function validates the length of `tokenIds` array. Further validation for `withdrawTokens` and `minWithdrawAmounts` ensuring their lengths match with `tokenIds` can be added.


    **poc**:
    ```solidity
    function rageQuit() external {
        require(tokenIds.length == withdrawTokens.length, "Length mismatch");
        require(tokenIds.length == minWithdrawAmounts.length, "Length mismatch");
        
    }
    ```

L43 - A check to ensure `receiver` is not a zero address can be added.


    **poc**:
    ```solidity
    function rageQuit() external {
        require(receiver != address(0), "Receiver cannot be zero address");
        
    }
    ```

L44 - In `transferFrom` and both `safeTransferFrom` functions, additional input validation for `owner`, `to`, and `tokenId` can be added.


    **poc**:
    ```solidity
    function transferFrom(address owner, address to, uint256 tokenId) public override {
        require(_exists(tokenId), "Token does not exist");
        require(owner != address(0), "Owner is zero address");
        require(to != address(0), "Transfer to zero address");
        
    }

    function safeTransferFrom(address owner, address to, uint256 tokenId) public override {
        require(_exists(tokenId), "Token does not exist");
        require(owner != address(0), "Owner is zero address");
        require(to != address(0), "Transfer to zero address");
        
    }

    function safeTransferFrom(address owner, address to, uint256 tokenId, bytes calldata data) public override {
        require(_exists(tokenId), "Token does not exist");
        require(owner != address(0), "Owner is zero address");
        require(to != address(0), "Transfer to zero address");
        
    }
    ```

L45 - In `addAuthority`, a check to ensure `authority` is not a zero address can be added.


    **poc**:
    ```solidity
    function addAuthority(address authority) external onlySelf {
        require(authority != address(0), "Authority is zero address");
        
    }
    ```

L46  - In the `initialize` function, additional validation for `initializeData` could be added to ensure it decodes correctly to `ProposalEngineOpts`.

    **poc**:
    ```solidity
    ProposalEngineOpts memory opts;
    if (initializeData.length > 0) {
        opts = abi.decode(initializeData, (ProposalEngineOpts));
    }
    ```

L47  - In `_initProposalImpl`, add a check to ensure the `impl` (implementation) address is not zero.

    **poc**:
    ```solidity
    function _initProposalImpl(IProposalExecutionEngine impl, bytes memory initData) internal {
        require(address(impl) != address(0), "Implementation address is zero");
        
    }
    ```

L48 - When setting the `signatureValidator`, ensure it is not the zero address.

    **poc**:
    ```solidity
    require(address(data.signatureValidator) != address(0), "Validator address is zero");
    _getSetSignatureValidatorProposalStorage().signatureValidators[data.signatureHash] = data.signatureValidator;
    ```

L49 - In `getSignatureValidatorForHash`, validate that `hash` is not zero.

    **poc**:
    ```solidity
    function getSignatureValidatorForHash(bytes32 hash) public view returns (IERC1271) {
        require(hash != bytes32(0), "Hash is zero");
        
    }
    ```
    
L50 - In the `setSigningThresholdBps` function, there could be validation to ensure `thresholdBps` is within a valid range.

    **poc**:
    ```solidity
    function setSigningThresholdBps(uint96 thresholdBps) external {
        require(thresholdBps <= 10000, "Invalid threshold"); // Assuming 100% as 10000 bps
        
    }
    ```

