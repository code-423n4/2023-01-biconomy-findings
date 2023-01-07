### Total L issues

| Number | Issues Details                                                                     | Context |
|--------|------------------------------------------------------------------------------------|---------|
| [L-01] | Low level calls with solidity version 0.8.14 and lower can result in optimiser bug |         |
| [L-02] | Critical changes should use-two step procedure                                     |  1      |
| [L-03] | Lack of event emit                                                                 |  2      |
| [L-04] | `initialize()` function can be called by anyone                                    |  2      |
| [L-05] | Avoid using `tx.origin`                                                            |  4      |
| [L-06] | Require messages are too short and unclear                                         |  5      |
| [L-07] | Unused `receive()` Function Will Lock Ether In Contract                            |  2      |

### Total NC issues

| Number  | Issues Details                                                                     | Context |
|---------|------------------------------------------------------------------------------------|---------|
| [NC-01] | Open TODO                                                                          |  1      |
| [NC-02] | Include return parameters in NatSpec comments                                      |         |
| [NC-03] | Non-usage of specific imports                                                      |         |
| [NC-04] | Lines are too long                                                                 |  14     |
| [NC-05] | Use `bytes.concat()`                                                               |  10     |
| [NC-06] | Use require instead of assert                                                      |  2      |
| [NC-07] | Typos                                                                              |  1      |


## [L-1] Low level calls with solidity version 0.8.14 and lower can result in optimiser bug

The protocol is using low level calls with solidity version 0.8.12 which can result in optimizer bug.

> Ref: https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

### Recommended Mitigation Steps

Consider upgrading to solidity 0.8.17

## [L-2] Critical changes should use-two step procedure

### Lines of code
- [SmartAccount.sol:109](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109-L114)

### Recommended Mitigation Steps 

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

## [L-3] Lack of event emit

The below methods do not emit an event when the state changes, something that it's very important for dApps and users.

- [BasePaymaster.sol:24](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24-L26)
- [VerifyingSingletonPaymaster.sol:65](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65-L68)

## [L-4] `initialize()` function can be called by anyone

`initialize()` function can be called anyone when the contract is not initialized

### Lines of code

- [SmartAccount.sol:166](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166)
- [SimpleAccount.sol:80](https://github.com/eth-infinitism/account-abstraction/blob/develop/contracts/samples/SimpleAccount.sol#L80)

### Recommended Mitigation Steps

Add a `deployer` address and require that only him can call the `initialize()` function.

## [L-5] Avoid using `tx.origin`

`tx.origin` is a global variable in Solidity that returns the address of the account that sent the transaction.

Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.

### Lines of code

- [SmartAccount.sol:257](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L257)
- [SmartAccount.sol:281](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L281)
- [SmartAccount.sol:511](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L511)
- [SimpleAccount.sol:113](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol#L113)

## [L-6] Require messages are too short and unclear

The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features of DApps like Tenderly. Error definitions should be added to the require block, not exceeding 32 bytes.

- [SmartAccount.sol:371](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L371)
- [SmartAccount.sol:528](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L528)
- [BasePaymaster.sol:105](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L105)
- [SimpleAccount.sol:139](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol#L139)
- [Math.sol:78](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L78)

## [L-7] Unused `receive()` Function Will Lock Ether In Contract

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert.

### Lines of code

- [SmartAccount.sol:550](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550)
- [SimpleAccount.sol:41](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol#L41)

### Recommended Mitigation Steps
The function should call another function, otherwise it should revert.

## [NC-1] Open TODO 

The code that contains "open todos" reflects that the development is not finished and that the code can change a posteriori, prior release, with or without audit.

- [EntryPoint.sol:255](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255)

## [NC-2] Include return parameters in NatSpec comments

If Return parameters are declared, you must prefix them with `/// @return`.
Some code analysis programs do analysis by reading [NatSpec](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html) details, if they can't see the `@return` tag, they do incomplete analysis.

## [NC-3] Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace. Instead, the Solidity docs recommend specifying imported symbols explicitly. 

- https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

### Recommended Mitigation Steps

Use specific imports syntax per solidity docs recommendation.

## [NC-4] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.

> Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

### Lines of code

- [SmartAccount.sol:46](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L46)
- [SmartAccount.sol:186](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L186)
- [SmartAccount.sol:239](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L239)
- [SmartAccount.sol:346](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L346)
- [SmartAccount.sol:356](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L356)
- [SmartAccount.sol:357](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L357)
- [SmartAccount.sol:489](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489)
- [EntryPoint.sol:168](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L168)
- [EntryPoint.sol:289](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L289)
- [EntryPoint.sol:319](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L319)
- [/EntryPoint.sol:349](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L349)
- [EntryPoint.sol:363](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L363)
- [EntryPoint.sol:401](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L401)
- [EntryPoint.sol:409](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L409)
- [EntryPoint.sol:440](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L440)

## [NC-5] Use `bytes.concat()`

Solidity version 0.8.4 introduces [`bytes.concat()`](https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=bytes.concat#the-functions-bytes-concat-and-string-concat) vs `abi.encodePacked(<bytes>,<bytes>)`

### Lines of code

- [SmartAccount.sol:294](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L294)
- [SmartAccount.sol:347](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L347)
- [SmartAccount.sol:374](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L374)
- [SmartAccount.sol:445](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L445)
- [SmartAccountFactory.sol:34](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L34)
- [SmartAccountFactory.sol:35](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L35)
- [SmartAccountFactory.sol:54](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L54)
- [SmartAccountFactory.sol:69](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L69)
- [SmartAccountFactory.sol:70](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L70)
- [SmartAccountFactory.sol:71](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L71)

### Recommended Mitigation Steps

Use `bytes.concat()`

## [NC-6] Use require instead of assert

Assert should not be used except for tests, require should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as `request()/revert()` did.

The big difference between the two is that the `assert()` function when false, uses up all the remaining gas and reverts all the changes made.

Meanwhile, a `require()` statement when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

Assertion should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type `Panic(uint256)`. Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".

### Lines of code

- [Singleton.sol:13](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13)
- [Proxy.sol:16](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16)

### Recommended Mitigation Steps

Use `require()` instead of `assert()`

## [NC-7] Typos

- [SmartAccount.sol:74](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L74)
