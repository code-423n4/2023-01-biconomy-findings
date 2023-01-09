1) Typographical error: https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L74

2) Duplicate initialization check: https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L168

The `owner == address(0)` check is sufficient.

3) name mismatch for `pullTokens`

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455

The function should be named `pushTokens` because it doesn't execute a `transferFrom` but a `(safe)transfer`

4) SafeERC20 can be used as `using SafeERC20 for `IERC20`

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L457

Consider implementing the `using` syntax.

5) `Enum` already imported

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L4

It is not necessary to import `Enum`, since its already imported within `Executor`.

6) Missing comment for `getModulesPaginated`

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L114

The `getModulesPaginated` function works in descending order, however, this is not well documented. Users would expect this function to be working in ascending order.

Consider adding a comment to this function.

7) `setFallbackHandler` lacks a non-zero validation

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L26

Consider adding a non-zero validation for the function input.

8) `_defaultImpl` can never be changed

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L8

Consider removing the immutable keyword and add a setter function for the aforementioned variable for the case that the implementation needs to be changed.

9) Missing event for `deployWallet`

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L53

Consider adding an event for the aforementioned function.

10) Events for the `Executor` contract should have an indexed `to` parameter

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L9

Consider adding the `indexed` keyword to the `to` parameter.

11) Unused import: `import "../utils/Exec.sol";` 

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L17

Consider removing the unused import.

12) General structure: `EntryPoint``

The `EntryPoint` contract is written in the bad structure e.g. internal before public functions, variable declarations in the middle of the contract etc. 

This reduces readability for third-parties.

Consider refactoring the structure for the aforementioned contracts.

13) `handleOps` might run out of gas

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L68

The `handleOps` function loops twice over the `UserOperations` array, if this array ever becomes too large, the function will run out of gas.

Consider limiting the array to a reasonable length.

*The same applies for `handleAggregatedOps`

14) Missing non-zero validation for `setEntryPoint`

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24

Consider adding a non-zero validation for this function. 

15) Best practice: `using Address for address`

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L49

The contract should follow the best-practices and implement `using Address for address` to make the code cleaner and more readable.

16. [GLOBAL] Missing `_disableInitializer`

Currently, all implementation contracts lack the aforementioned function. It might make sense to call this function within the constructor to avoid an initialization of the implementation contract.