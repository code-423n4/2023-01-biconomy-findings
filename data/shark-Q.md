## 1. `Math.sol` function not being used

In `Math.sol`, the function [`max()`](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L19-L21) is found to be re-implemented in a contract.

The following contract is re-implementing `max()` from `Math.sol` rather than just importing it.

File: `SmartAccount.sol` [Line 181-183](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L181-L183)

## 2. Use `uint256` instead of `uint`

`uint` is just a shorthand of `uint256`. To be more explicit, consider replacing any instances of `uint` with `uint256` where possible.

- File: `SmartAccount.sol` [Line 449](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449)
- File: `SmartAccount.sol` [Line 468](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L468)
- File: `SmartAccountFactory.sol` [Line 72](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L72)

## 3. Use delete to clear variables instead of zero assignment

Using the [`delete`](https://docs.soliditylang.org/en/v0.8.17/types.html#delete) keyword more clearly states your intention.

For example:

File: `StakeManager.sol` [Line 86](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L86)

The instance above can be changed to:

```solidity
        delete info.staked;
```

## 4. Empty block statements

Empty block statements can cause confusion when reading code. It is recommended to write a clear comment for empty block statements or emit an event.

- File: `SmartAccount.sol` [Line 550](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550)

## 5, Open TODO

Open TODOs can hint at programming or architectural errors that have yet to be resolved.

- File: `EntryPoint.sol` [Line 255](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255)

## 6. Contract existence checks

A low-level call could return success on a non-existent contract even if no function call was executed.

For more info, see the following:
[docs.soliditylang.org/en/v0.8.7/control-structures.html#error-handling-assert-require-revert-and-exceptions](https://docs.soliditylang.org/en/v0.8.7/control-structures.html#error-handling-assert-require-revert-and-exceptions)

Here is an instance of this issue:

File: `SmartAccount.sol` [Line 449-453](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449-L453)

```solidity
449:    function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
450:        require(dest != address(0), "this action will burn your funds");
451:        (bool success,) = dest.call{value:amount}("");
452:        require(success,"transfer failed");
453:    }
```

Consider adding the following require statement before line 451. This will make sure the contract is valid:

```solidity
require(dest.isContract(), "Invalid contract");
```
