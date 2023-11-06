# OffChainSignatureValidator::isValidSignature is a view function that can revert

In Solidity, the `view` and `pure` function modifiers are used to indicate that a function does not modify the state of the contract. These functions are intended for querying data and should not have any side effects. 

It is generally considered good practice for view and pure functions to not revert. 

## Impact
This behavior inhibits external callers from custom error handling when invoking `isValidSignature`, which is present in [ProposalExecutionEngine.sol#L225](https://github.com/code-423n4/2023-10-party/blob/b23c65d62a20921c709582b0b76b387f2bb9ebb5/contracts/proposals/ProposalExecutionEngine.sol#L225). Additionally, it deviates from the `ERC1271` standard implementation, where the return value `magicValue` is reserved for conveying error or success messages. The expected behavior should be to determine whether the provided signature is valid for the given data, as outlined in OpenZeppelin's [specifications](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/94697be8a3f0dfcd95dfb13ffbd39b5973f5c65d/contracts/interfaces/IERC1271.sol#L12),rather than returning the function selector, as seen in this implementation.

## Mitigation 
To address this issue, it's recommended to modify the function to return an error message instead of reverting when the validation fails. Below is the suggested code modification:
```
  // we use a error to extract the selector later
  error isValidSignature();
  function isValidSignature(bytes32 hash, bytes memory signature) external view returns (bytes4) {
        uint8 v;
        bytes32 r;
        bytes32 s;
        assembly {
            // First word of signature after size contains r
            r := mload(add(signature, 0x20))
            s := mload(add(signature, 0x40))
            // v is one byte which starts after s. type is uint8 so extra data will be ignored
            v := mload(add(signature, 0x41))
        }

        bytes memory message;
        assembly {
            // Raw message data begins after v. Overwriting part of s and v with size of `message`
            message := add(signature, 0x41)
            mstore(message, sub(mload(signature), 0x41))
        }

        // Recreate the message pre-hash from the raw data
        bytes memory encodedPacket = abi.encodePacked(
            "\x19Ethereum Signed Message:\n",
            Strings.toString(message.length),
            message
        );
        if (keccak256(encodedPacket) != hash) {
            return MessageHashMismatch.selector;
        }

        Party party = Party(payable(msg.sender));
        address signer = ecrecover(hash, v, r, s);
        uint96 signerVotingPowerBps = party.getVotingPowerAt(signer, uint40(block.timestamp)) *
            10000;

        if (signerVotingPowerBps == 0 && party.balanceOf(signer) == 0) {
            // Must own a party card or be delegatated voting power
            return NotMemberOfParty.selector;
        }

        uint96 totalVotingPower = party.getGovernanceValues().totalVotingPower;
        uint96 thresholdBps = signingThersholdBps[party];

        // Either threshold is 0 or signer votes above threshold
        if (
            thresholdBps == 0 ||
            (signerVotingPowerBps > totalVotingPower &&
                signerVotingPowerBps / totalVotingPower >= thresholdBps)
        ) {
            return ValidSignature.selector;
        }

        return InsufficientVotingPower.selector;
    }
```