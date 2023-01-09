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

## 7. Spaced comment

Adding a space before a comment can make text easier to read, thus increasing readability.

- File: `BaseSmartAccount.sol` [Line 22](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L22)
- File: `BaseSmartAccount.sol` [Line 110](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L110)
- File: `EntryPoint.sol` [Line 144](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L144)
- File: `EntryPoint.sol` [Line 250](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L250)

## 8. Typo mistakes

It is recommended to use the proper spelling of words. Typos can make it difficult for readers to decipher the intended meaning, which can lead to confusion.

File: `SmartAccount.sol` ([Line 38](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L38), [Line 74](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L74), [Line 153](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L153), [Line 223](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L223), [Line 320](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L320))

```
       /// @audit Seperators -> Separators
38:    // Domain Seperators

       /// @audit Remove "an"                vv
74:    * @notice Throws if the sender is not an the owner.

        /// @audit transaction -> transactions
153:    * @return nonce : the number of transaction made within said batch

        /// @audit send -> sent                              vvvv
223:    // We also include the 1/64 in the check that is not send along with a call to counteract potential shortings because of EIP-150

        /// @ audit send -> sent                                                                                   vvvv
320:    // This check is not completely accurate, since it is possible that more signatures than the threshold are send.
```

File: `BaseSmartAccount.sol` [Line 110](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L110)

```
            /// @audit its -> (it's || it is)
110:        //ignore failure (its EntryPoint's job to verify, not account.)
```

File: `EntryPoint.sol` ([Line 43](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L43), [Line 222](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L222), [Line 277](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L277))

```
        /// @audit Remove redundancy: "into into"
43:     * @param opIndex into into the opInfo array

         /// @audit revert -> reverts
222:     * @dev this method always revert. Successful result is SimulationResult error. other errors are failures.

         /// @audit revert, -> reverts (remove the comma)
277:     * this method always revert, and returns the address in SenderAddressResult error
```

File: `ModuleManager.sol` [Line 103](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L103)

```
    /// @audit an -> a
    /// @dev Returns if an module is enabled
```

File: `MultiSendCallOnly.sol` [Line 17](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L17)

```
       /// @audit "for most part" -> "for the most part"
17:    /// @notice The code is for most part the same as the normal MultiSend (to keep compatibility),
```
