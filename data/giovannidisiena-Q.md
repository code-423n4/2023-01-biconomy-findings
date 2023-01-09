#### **[NC-01] Incomplete NatSpec**
##### **Context**:
Functions across multiple files.

##### **Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI) as stated in the official Solidity documentation.

##### **Recommendation**:
Add complete NatSpec (@notice/@param/@return) to all public interfaces across the repo.

#### **[NC-02] Proxy Naming**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L4-L8

##### **Description:**
This contract acts as a proxy for the user's account but could benefit from additional clarity in its naming.

##### **Recommendation**:
Rename `Proxy` to `SmartAccountProxy` and update the `_IMPLEMENTATION_SLOT` hash.

#### **[NC-03] Checks-Effects-Interactions When Emitting Events**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109-L114

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L116-L125

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L59-L74

##### **Description:**
As is done in the `updateEntryPoint` function, it is recommended to follow the checks-effects-interaction pattern. This is usually to safeguard against unsafe external calls and re-entrancy but is still good practice as issues with events can cause problems for off-chain infrastructure such as indexers.

##### **Recommendation**:
Emit events prior to making any state changes.

#### **[NC-04] Incorrect Revert String
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L170-L171

##### **Description:**
This function should revert if the `_handler` parameter is the zero address; however, there appears to be a simple copy-paste error.

##### **Recommendation**:
Update the revert string to "Invalid Handler".

#### **[NC-05] Duplicated Check
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171C5-L176

##### **Description:**
Duplicated check for `_handler` being equal to the zero address is not necessary.

##### **Recommendation**:
Given the input has already been validated, remove the unnecessary if statement.

#### **[NC-06] Use Math Library Functions**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L178-L183

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L496-L498

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L77-L79

##### **Description:**
Multiple `min` and `max` functions are defined and inlined accross multiple contracts.

##### **Recommendation**:
For readability, use `libs/Math.sol` library functions rather than inlining. Additionally, the version of `max` in `SmartAccount` unnecessarily uses `>=` which gives the same return values as `>` as in the library.

#### **[NC-07] Use of Old OpenZeppelin `Math.sol` Version**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

##### **Description:**
This repo uses v4.7.0 of the library.

##### **Recommendation**:
Ensure that copied library contracts are up-to-date or alternatively import as a dependency. This version is missing a revert string "Math: mulDiv overflow" on #L78 and should read "base 256" on #L336.

#### **[NC-08] Remove Unnecessary Parentheses**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L264

##### **Description:**
Precedence of arithmetic operators means that the final two sets of parentheses here are redundant.

##### **Recommendation**:
`payment = (gasUsed + baseGas) * gasPrice / tokenGasPriceFactor;`

#### **[NC-09] Fix Indentation**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L314-L344

##### **Description:**
Correct formatting and folling of Solidity style guidelines helps with readability and auditing. There appears to be an additional indentation within part of the `if` statement.

##### **Recommendation**:
Remove the extra indentation to aid in readability of `if/else` block.

#### **[NC-10] Remove References to Gnosis Safe and Wallet**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L429

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L20

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L240

##### **Description:**
Given some of the code has been forked from Gnosis Safe and Eth-Infinitism, there remain some relic references. For example, the `VerifyingSingletonPaymaster` should read 'the wallet signs to prove identity and account ownership'.

##### **Recommendation**:
Remove references to 'safe' and 'wallet' and change to 'account'.

#### **[NC-11] Return `sigTimeRange` as per EIP-4337 Updates**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L27

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L29

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L98

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L25

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol#L36

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol#L62

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L61

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L507

##### **Description:**
The return value is packed of sigFailure, validUntil and validAfter timestamps. sigFailure is 1 byte value of “1” the signature check failed (should not revert on signature failure, to support estimate). validUntil is 8-byte timestamp value, or zero for “infinite”. The UserOp is valid only up to this time. validAfter is 8-byte timestamp. The UserOp is valid only after this time.

##### **Recommendation**:
// TODO

#### **[NC-12] Revert as per EIP-4337 Updates**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L507-L512

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L108

##### **Description:**
should return SIG_VALIDATION_FAILED as per EIP and all other errors should revert

##### **Recommendation**:
// TODO

#### **[NC-13] Prevent Singleton Receiving Ether**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L549-L550

##### **Description:**
The `SmartAccount` singleton should not be able to receive ether.

##### **Recommendation**:
Remove the `receive` function. Proxied SmartAccounts already have a receive function.

#### **[NC-14] Singleton Naming Consistency**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

##### **Description:**
In other contracts such as `SmartAccount` and `Singleton` there are references to singleton rather than defaultImpl.

##### **Recommendation**:
Rename `_deafaultImpl` to `s_singleton` to remain consistent with the naming in `SmartAccount` contract.

#### **[NC-15] Explicit Import Naming**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol#L8-L17

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L27

##### **Description:**
Many of the contracts import several other contracts so it can be difficult to track down from where specific items are being imported. For example, consider this use of `UserOperationLib` in `BaseAccount` implicitly imported from `IAccount`.

##### **Recommendation**:
For the sake of readability, considering using explicit imports of the form `import {X, Y, Z} from "A"`.

#### **[NC-16] Remove Unused Imports**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L17

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L19

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L7

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L10

##### **Description:**
// TODO

##### **Recommendation**:
Remove the `Exec.sol`, `ECDSA.sol`, `Initializable.sol` and `Signatures.sol` imports, in addition to the `using for` directive in `PaymasterHelpers.sol`.

#### **[NC-17] Fix Incorrect Calculation Comment**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L49

##### **Description:**
The `DepositInfo` struct is commented to state that 112 bit allows for 2^15 eth; however, this calculation is not correct: (2^112 - 1)/1e18 = 5e15

##### **Recommendation**:
Update the comment to 5e15

#### **[NC-18] Share Proxy/Singleton Storage**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L4-L10

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L4-L11

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L122

##### **Description:**
Code is duplicated when using EIP-1967 storage at the same slot across both `Proxy` and `Singleton`.

##### **Recommendation**:
Make use of a storage library which can have an internal function to be called by `SmartAccount` on #L122.

#### **[NC-19] Remove Inaccurate Storage Comment**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L4-L10

##### **Description:**
Given the `Singleton` contract is utilising EIP-1967 storage, the comments regarding order of variable declaration affecting storage location are not accurate. The position of implementation address in storage does not follow Solidity's rules for storage layout and is instead fixed at the `_IMPLEMENTATION_SLOT`.

##### **Recommendation**:
Remove these comments (as they pertain to the Gnosis Safe contracts).

#### **[NC-20] Combine `_getImplementation` With Proxy Fallback**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L20-L25

##### **Description:**
It is possible to combine this function call with the `Proxy` fallback function, in addition to the recommendation above removing the need for `Singleton` altogether.

##### **Recommendation**:
Integrate this call with the `Proxy` fallback at no additional cost, as is done by `GnosisSafeProxy`: https://github.com/safe-global/safe-contracts/blob/main/contracts/proxies/GnosisSafeProxy.sol#L31-L34. The difference will be in accessing `_IMPLEMENTATION_SLOT` directly.

#### **[NC-21] Remove Unused IERC1271Wallet Interface**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol

##### **Description:**
As suggested by the comment on #L4, this interface is not used.

##### **Recommendation**:
Remove the interface.

#### **[NC-22] Remove Unused Variable Workaround for Variable that is Used**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L99

##### **Description:**
This workaround is included to silence unused variable warnings; however, `requiredPrefund` is in fact used on #L109.

##### **Recommendation**:
Remove #L99.

#### **[NC-23] Remove Obsolete/Repeated Comments**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L125

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L8

##### **Description:**
Obsolete comments affect readability.

##### **Recommendation**:
Remove comments which are repeated or no longer relevant.

#### **[NC-24] Inconsistent Storage VariableNaming**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L55-L59

##### **Description:**
The naming convention of storage variables even within a single contract is inconsistent and affects readability.

##### **Recommendation**:
Consider marking all storage variables with `s_prefix` to be explicit when accessing storage.

#### **[NC-24] Inconsistent Storage VariableNaming**
##### **Context**:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L348-L353

##### **Description:**
The `_signer` validation is duplicated. 

##### **Recommendation**:
Validate only once after all code paths.