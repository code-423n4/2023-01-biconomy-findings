
## 01 require() should be used instead of assert()

Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process’s available gas instead of returning it, as request()/revert() did.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states “The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there’s a mistake”.

_There are 2 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol

```
File: contracts/smart-contract-wallet/Proxy.sol

16: assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol

```
File: contracts/smart-contract-wallet/common/Singleton.sol

13: assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```

------

## 02 Avoid using tx.origin

`tx.origin` is a global variable in Solidity that returns the address of the account that sent the transaction.

Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.

This can make it easier to create a vault on behalf of another user with an external administrator (by receiving it as an argument).

_There are 3 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```
File: contracts/smart-contract-wallet/SmartAccount.sol

257: address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
281: address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
511: require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
```

---------

## 03 Upgradeable contract is missing a \_\_gap[50] storage variable to allow for new storage variables in later versions

See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

_There is 1 instance of this issue:_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```
File: contracts/smart-contract-wallet/SmartAccount.sol

28: ReentrancyGuardUpgradeable
```

---------

## 04 Use scientific notation rather than exponentiation

Use scientific notation (e.g. 1e18) rather than exponentiation (e.g. `10**18`)

Scientific notation should be used for better code readability

_There are 14 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

```
File: contracts/smart-contract-wallet/libs/Math.sol

261: if (value >= 10**64) {
262: value /= 10**64;
265: if (value >= 10**32) {
266: value /= 10**32;
269: if (value >= 10**16) {
270: value /= 10**16;
273: if (value >= 10**8) {
274: value /= 10**8;
277: if (value >= 10**4) {
278: value /= 10**4;
281: if (value >= 10**2) {
282: value /= 10**2;
285: if (value >= 10**1) {
299: return result + (rounding == Rounding.Up && 10**result < value ? 1 : 0);
```

-----------

## 05 Best practice is to prevent signature malleability

Use OpenZeppelin’s `ECDSA` contract rather than calling `ecrecover()` directly

_There are 2 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```
File: contracts/smart-contract-wallet/SmartAccount.sol

347: _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);
350: _signer = ecrecover(dataHash, v, r, s);
```

--------

## 06 Open TODOS

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

_There is 1 instance of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

255: // TODO: copy logic of gasPrice?
```

----------

## 07 Missing events for functions that change critical parameters

The afunctions that change critical parameters should emit events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

Missing events and timelocks do not promote transparency and if such changes immediately affect users’ perception of fairness or trustworthiness, they could exit the protocol causing a reduction in liquidity which could negatively impact protocol TVL and reputation.

_There are 3 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol

```
File: contracts/smart-contract-wallet/common/Singleton.sol

12: function _setImplementation(address _imp) internal {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

```
File: contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

24: function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

```
File: contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

65: function setSigner( address _newVerifyingSigner) external onlyOwner{
```

### Recommended Mitigation Steps

Add events to all functions that change critical parameters.

-----------

## 08 Typos

_There are 6 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

43: * @param opIndex into into the opInfo array
144: //a memory copy of UserOp fields (except that dynamic byte arrays: callData, initCode and signature
250: //when using a Paymaster, the verificationGasLimit is used also to as a limit for the postOp call.
```

Typo: In line 43, the word `into` has been repeated
In line 144, the parenthesis is not closed
In line 250, it should be `is used to` instead of `is used also to`

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol

```
File: contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol

10:  * - the validateUserOp MUST valiate the aggregator parameter, and MAY ignore the userOp.signature field.
```

Typo: It should be `validate` instead of `valiate`

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol

```
File: contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol

20: * First it validates the signature over the userOp. then it return data to be used when creating the handleOps:
31: * bundler MAY use optimized custom code perform this aggregation
```

Typo: In line 20, it should be `returns` instead of `return`
In line 31, it should be `to perform` instead of `perform`

----

## 09 Don't use periods with fragments

Throughout the files, most of the comments have fragments that end with periods. They should either be converted to actual sentences with both a noun phrase and a verb phrase, or the periods should be removed.

_There are 45 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```
File: contracts/smart-contract-wallet/SmartAccount.sol

355: /// @dev Allows to estimate a transaction.
358: /// @param to Destination address of Safe transaction.
359: /// @param value Ether value of transaction.
360: /// @param data Data payload of transaction.
361: /// @param operation Operation type of transaction.
362: /// @return Estimate without refunds and overhead fees (base transaction and payload data gas costs).
377: /// @dev Returns hash to be signed by owner.
378: /// @param to Destination address.
379: /// @param value Ether value.
380: /// @param data Data payload.
381: /// @param operation Operation type.
382: /// @param targetTxGas Fas that should be used for the safe transaction
383: /// @param baseGas Gas costs for data used to trigger the safe transaction.
384: /// @param gasPrice Maximum gas price that should be used for this transaction.
385: /// @param gasToken Token address (or 0 if ETH) that is used for the payment.
386: /// @param refundReceiver Address of receiver of gas payment (or 0 if tx.origin).
387: /// @param _nonce Transaction nonce.
388: /// @return Transaction hash.
419: /// @dev Returns the bytes that are hashed to be signed by owner.
422: /// @param _nonce Transaction nonce.
423: /// @return Transaction hash bytes.
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

508: //place the NUMBER opcode in the code.
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

```
File: contracts/smart-contract-wallet/base/FallbackManager.sol

22: /// @dev Allows to add a contract to handle fallback calls.
25: /// @param handler contract to handle fallback calls.
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

```
File: contracts/smart-contract-wallet/base/ModuleManager.sol

28: /// @dev Allows to add a module to the whitelist.
29: ///      This can only be done via a Safe transaction.
30: /// @notice Enables the module `module` for the Safe.
31: /// @param module Module to be whitelisted.
42: /// @dev Allows to remove a module from the whitelist.
43: ///      This can only be done via a Safe transaction.
44: /// @notice Disables the module `module` for the Safe.
46: /// @param module Module to be removed.
56: /// @dev Allows a Module to execute a Safe transaction without any further confirmations.
57: /// @param to Destination address of module transaction.
58: /// @param value Ether value of module transaction.
59: /// @param data Data payload of module transaction.
60: /// @param operation Operation type of module transaction.
76: /// @param to Destination address of module transaction.
77: /// @param value Ether value of module transaction.
78: /// @param data Data payload of module transaction.
79: /// @param operation Operation type of module transaction.
110: /// @param start Start of the page
111: /// @param pageSize Maximum number of modules that should be returned.
112: /// @return array Array of modules.
113: /// @return next Start of the next page.
```

-----------

## 10 Use named imports instead of plain 'import file.sol'

For instance, you use regular imports such as:

[BaseSmartAccount.sol#L8-L10](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L8-L10)

```
8: import "@account-abstraction/contracts/interfaces/IAccount.sol";
9: import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";
10: import "./common/Enum.sol";
```

Instead of this, use named imports such as:

```
8: import {IAccount.sol} from "@account-abstraction/contracts/interfaces/IAccount.sol";
9: import {IEntryPoint.sol} "@account-abstraction/contracts/interfaces/IEntryPoint.sol";
10: import {Enum.sol} "./common/Enum.sol";
```

_There are 161 instances of this issue in the following In-scope contracts:_

[BaseSmartAccount.sol#L8-L10](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L8-L10), [SmartAccount.sol#L4-L16](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L4-L16), [SmartAccountFactory.sol#L4-L5](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L4-L5), [EntryPoint.sol#L12-L19](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L12-L19), [StakeManager.sol#L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L4), [IAccount.sol#L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L4), [IAggregatedAccount.sol#L4-L6](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L4-L6), [IAggregator.sol#L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L4), [IEntryPoint.sol#L12-L14](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L12-L14), [Executor.sol#L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L4), [FallbackManager.sol#L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L4) , [ModuleManager.sol#L4-L6](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L4-L6), [DefaultCallbackHandler.sol#L4-L7](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L4-L7), [BasePaymaster.sol#L7-L9](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L7-L9), [PaymasterHelpers.sol#L4-L5](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L4-L5), [VerifyingSingletonPaymaster.sol#L5-L9](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L5-L9)

-------------

## 11 Use a more recent version of solidity

It's a best practice to use the latest compiler version.

The specified minimum compiler version is quite old. Older compilers might be susceptible to some bugs. We recommend changing the solidity version pragma to the latest version to enforce the use of an up to date compiler.

List of known compiler bugs and their severity can be found here: [https://etherscan.io/solcbuginfo](https://etherscan.io/solcbuginfo)

This issue exists in all the In-scope contracts

----------
