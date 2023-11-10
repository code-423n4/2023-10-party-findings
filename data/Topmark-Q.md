### Report 1:
## Title
#### Conflict due to difference in mint Function Implementation and User possible Function usage intention would cause Informational Errors and Data Errors to Users
## Impact
If User or mint function caller intends to re-delegate to a new delegate since it is in the mint parameter and the function ends up delegating back to old delegate, It would affect User interpretation of its current voting power which might not end up meeting users expectation in terms of voting right execution and could escalate due to wrong information.
## Proof of Concept
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L197
```solidity
    /// @notice Mint a governance NFT for `owner` with `votingPower` and
    ///         immediately delegate voting power to `delegate.` Only callable
    ///         by an authority.
    /// @param owner The owner of the NFT.
    /// @param votingPower The voting power of the NFT.
>>> /// @param delegate The address to delegate voting power to.
    function mint(
        address owner,
        uint256 votingPower,
        address delegate
    ) external returns (uint256 tokenId) {
...
>>>     // Use delegate from party over the one set during crowdfund.
        address delegate_ = delegationsByVoter[owner];
        if (delegate_ != address(0)) {
            delegate = delegate_;
        }
```
As seen in the mint(...) function above "delegate" is one of the parameters a user can input to call the function, a user could fall victim of thinking the mint function can be used to re-delegate along side minting, but as later noted in the code provided above the function silently reassigns the delegate user inputted back to the old delegate.
## Tools Used
Manual Review
## Recommended Mitigation Steps
Instead of silently changing delegate back to previous delegate even though the User Probably intends to change the delegate to a new one, it is better to revert the code with right information error so that User would not be caught unaware that their Voting power was not re-delegated to a new one. So that Users can re-delegate the proper way before calling the mint function
```solidity
    /// @notice Mint a governance NFT for `owner` with `votingPower` and
    ///         immediately delegate voting power to `delegate.` Only callable
    ///         by an authority.
    /// @param owner The owner of the NFT.
    /// @param votingPower The voting power of the NFT.
    /// @param delegate The address to delegate voting power to.
    function mint(
        address owner,
        uint256 votingPower,
        address delegate
    ) external returns (uint256 tokenId) {
...
---     // Use delegate from party over the one set during crowdfund.
        address delegate_ = delegationsByVoter[owner];
        if (delegate_ != address(0)) {
+++     Revert ( "Cannot Re-delegate during Minting" ); // this is safer for the user 
---     delegate = delegate_;
        }
```
###  Report 2:
#### Absence of validation before setting fundingSplitBps
Critical analysis shows that the maximum value for fundingSplitBps in the ETHCrowdfundBase.sol contract is 1e4, but no validation was done at L168 of the contract before it was assigned a value, necessary validation should be done as provided below
https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/ETHCrowdfundBase.sol#L168
```solidity
 function _initialize(ETHCrowdfundOptions memory opts) internal {
   ...
     // Set the funding split and its recipient.
        fundingSplitBps = opts.fundingSplitBps;
+++     require( opts.fundingSplitBps <= 1e4, "invalid fundingSplitBps" )
  ...
}
```
### Report 3:
## Title
#### Contradictory function implementation in the rageQuit(...) function of the PartyGovernanceNFT contract.
## Impact
rageQuit(...) function is suppose to revert if "withdrawAmounts" is lower than "minWithdrawAmounts" but would not revert if "withdrawAmounts" is exactly equal to zero even if this value(i.e zero) is lower than "minWithdrawAmounts" due to contradictory function implementation.
## Proof of Concept
https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L426
```solidity
     function rageQuit(
        uint256[] calldata tokenIds,
        IERC20[] calldata withdrawTokens,
        uint256[] calldata minWithdrawAmounts,
        address receiver
    ) external {
        ...
         for (uint256 i; i < withdrawTokens.length; ++i) {
                IERC20 token = withdrawTokens[i];
                uint256 amount = withdrawAmounts[i];
    // Take fee from amount.
                uint256 fee = (amount * feeBps_) / 1e4;

                if (fee > 0) {
                    amount -= fee;

                    // Transfer fee to fee recipient.
                    if (address(token) == ETH_ADDRESS) {
                        payable(feeRecipient).transferEth(fee);
                    } else {
                        token.compatTransfer(feeRecipient, fee);
                    }
                }

    >>>            if (amount > 0) {
                 uint256 minAmount = minWithdrawAmounts[i];

                    // Check amount is at least minimum.
     >>>               if (amount < minAmount) {
                        revert BelowMinWithdrawAmountError(amount, minAmount);
                    }
```
As shown in the code provided from the rageQuit(...) function above if amount is zero the code does not bother to check if amount  is not below "minAmount" when in actual fact it is below "minAmount". A bad actor can take advantage of this by simply calculating the expected result of amount after fee has been subtracted at [L416](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L416) to make sure it would be equal zero instead of the actual amount, this way the validation at [L430](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol#L430) to ensure it is not below minAmount will not be done.
## Tools Used
Manual Review
## Recommended Mitigation Steps
There is no need to check if amount greater than zero before requiring that amount is not below minWithdrawAmounts i.e the contradictory check of "amount > 0" should be removed as done below
```solidity
     function rageQuit(
        uint256[] calldata tokenIds,
        IERC20[] calldata withdrawTokens,
        uint256[] calldata minWithdrawAmounts,
        address receiver
    ) external {
        ...
         for (uint256 i; i < withdrawTokens.length; ++i) {
                IERC20 token = withdrawTokens[i];
                uint256 amount = withdrawAmounts[i];

             ...

---             if (amount > 0) {
                 uint256 minAmount = minWithdrawAmounts[i];

                    // Check amount is at least minimum.
                    if (amount < minAmount) {
                        revert BelowMinWithdrawAmountError(amount, minAmount);
                    }
              ...
---          }
```