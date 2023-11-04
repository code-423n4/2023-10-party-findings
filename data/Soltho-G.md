# ETH transfers gas optimization

The current function that transfer ETH looks like this:

```solidity
    error EthTransferFailed(address receiver, bytes errData);

    // Transfer ETH with full gas stipend.
    function transferEth(address payable receiver, uint256 amount) internal {
        if (amount == 0) return;

        (bool s, bytes memory r) = receiver.call{ value: amount }("");
        if (!s) {
            revert EthTransferFailed(receiver, r);
        }
    }
```
But it could be further optimized using inline assembly:

```solidity
    error EthTransferFailed(); // remove error args for more optimization

    // Transfer ETH with full gas stipend.
    function transferEth(address payable receiver, uint256 amount) internal {
          /// @solidity memory-safe-assembly
        assembly {
            if iszero(call(gas(), receiver, amount, codesize(), 0x00, codesize(), 0x00)) {
                mstore(0x00, 0x6d963f88) // `ETHTransferFailed()`.
                revert(0x1c, 0x04)
            }
        }
    }
```solidity