## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | `internal` functions only called once can be inlined to save gas | 9 |  180 |
| [G&#x2011;02] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 2 |  170 |
| [G&#x2011;03] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 5 |  300 |
| [G&#x2011;04] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 1 |  - |
| [G&#x2011;05] | `keccak256()` should only need to be called on a specific string literal once | 1 |  42 |
| [G&#x2011;06] | Optimize names to save gas | 21 |  462 |
| [G&#x2011;07] | `>=` costs less gas than `>` | 1 |  3 |
| [G&#x2011;08] | Splitting `require()` statements that use `&&` saves gas | 3 |  9 |
| [G&#x2011;09] | `require()` or `revert()` statements that check input arguments should be at the top of the function | 6 |  - |
| [G&#x2011;10] | Functions guaranteed to revert when called by normal users can be marked `payable` | 4 |  84 |

Total: 53 instances over 10 issues with **1250 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 9 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

203:      function _copyUserOpToMemory(UserOperation calldata userOp, MemoryUserOp memory mUserOp) internal pure {

248:      function _getRequiredPrefund(MemoryUserOp memory mUserOp) internal view returns (uint256 requiredPrefund) {

261:      function _createSenderIfNeeded(uint256 opIndex, UserOpInfo memory opInfo, bytes calldata initCode) internal {

289       function _validateAccountPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, address aggregator, uint256 requiredPrefund)
290:      internal returns (uint256 gasUsedByValidateAccountPrepayment, address actualAggregator, uint256 deadline) {

349:      function _validatePaymasterPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, uint256 requiredPreFund, uint256 gasUsedByValidateAccountPrepayment) internal returns (bytes memory context, uint256 deadline) {

496:      function min(uint256 a, uint256 b) internal pure returns (uint256) {

500:      function getOffsetOfMemoryBytes(bytes memory data) internal pure returns (uint256 offset) {

504:      function getMemoryBytesFromOffset(uint256 offset) internal pure returns (bytes memory data) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L203

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

181:      function max(uint256 a, uint256 b) internal pure returns (uint256) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L181

### [G&#x2011;02]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There are 2 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

/// @audit require() on line 353
354:          uint256 gas = verificationGasLimit - gasUsedByValidateAccountPrepayment;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L354

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

/// @audit require() on line 117
118:          info.deposit = uint112(info.deposit - withdrawAmount);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L118

### [G&#x2011;03]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 5 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

100:          for (uint256 i = 0; i < opasLen; i++) {

107:          for (uint256 a = 0; a < opasLen; a++) {

112:              for (uint256 i = 0; i < opslen; i++) {

128:          for (uint256 a = 0; a < opasLen; a++) {

134:              for (uint256 i = 0; i < opslen; i++) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100

### [G&#x2011;04]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

42:           revert("Deposit must be for a paymasterId. Use depositFor");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L42

### [G&#x2011;05]  `keccak256()` should only need to be called on a specific string literal once
It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to `bytes4` should also only be done once

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol

13:           assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13

### [G&#x2011;06]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 21 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

/// @audit handleOps(), handleAggregatedOps(), innerHandleOp(), getUserOpHash(), simulateValidation(), getSenderAddress()
21:   contract EntryPoint is IEntryPoint, StakeManager {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L21

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol

/// @audit createSender()
8:    contract SenderCreator {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L8

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

/// @audit getDepositInfo(), depositTo(), addStake(), unlockStake(), withdrawStake(), withdrawTo()
13:   abstract contract StakeManager is IStakeManager {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L13

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol

/// @audit validateUserOp()
5:    interface IAccount {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L5

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol

/// @audit getAggregator()
12:   interface IAggregatedAccount is IAccount {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L12

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol

/// @audit validateSignatures(), validateUserOpSignature(), aggregateSignatures()
9:    interface IAggregator {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L9

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol

/// @audit handleOps(), handleAggregatedOps(), getUserOpHash(), simulateValidation(), getSenderAddress()
16:   interface IEntryPoint is IStakeManager {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L16

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol

/// @audit validatePaymasterUserOp(), postOp()
10:   interface IPaymaster {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L10

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol

/// @audit getDepositInfo(), depositTo(), addStake(), unlockStake(), withdrawStake(), withdrawTo()
9:    interface IStakeManager {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L9

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

/// @audit setFallbackHandler()
8:    contract FallbackManager is SelfAuthorized {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L8

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

/// @audit enableModule(), disableModule(), execTransactionFromModule(), execTransactionFromModuleReturnData(), isModuleEnabled(), getModulesPaginated()
9:    contract ModuleManager is SelfAuthorized, Executor {    

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L9

```solidity
File: scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

/// @audit nonce(), nonce(), entryPoint(), init(), execTransaction()
33:   abstract contract BaseSmartAccount is IAccount {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L33

```solidity
File: scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol

/// @audit tokensReceived()
4:    interface ERC777TokensRecipient {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol

/// @audit isValidSignature()
5:    interface IERC1271Wallet {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol#L5

```solidity
File: scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol

/// @audit isValidSignature()
9:    abstract contract ISignatureValidator is ISignatureValidatorConstants {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol#L9

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol

/// @audit multiSend()
8:    contract MultiSendCallOnly {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L8

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol

/// @audit multiSend()
9:    contract MultiSend {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L9

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

/// @audit setEntryPoint(), withdrawTo(), addStake(), getDeposit(), unlockStake(), withdrawStake()
16:   abstract contract BasePaymaster is IPaymaster, Ownable {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L16

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

/// @audit depositFor(), setSigner(), getHash()
22:   contract VerifyingSingletonPaymaster is BasePaymaster {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L22

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

/// @audit deployCounterFactualWallet(), deployWallet(), getAddressForCounterfactualWallet()
7:    contract SmartAccountFactory {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L7

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit setOwner(), updateImplementation(), updateEntryPoint(), domainSeparator(), getChainId(), getNonce(), handlePaymentRevert(), checkSignatures(), requiredTxGas(), getTransactionHash(), encodeTransactionData(), transfer(), pullTokens(), execute(), executeBatch(), execFromEntryPoint(), getDeposit(), addDeposit(), withdrawDepositTo()
18:   contract SmartAccount is 

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L18

### [G&#x2011;07]  `>=` costs less gas than `>`
The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, [which saves **3 gas**](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde)

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

20:           return a > b ? a : b;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L20

### [G&#x2011;08]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There are 3 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

34:           require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

49:           require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

68:           require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34

### [G&#x2011;09]  `require()` or `revert()` statements that check input arguments should be at the top of the function
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (**2100 gas***) in a function that may ultimately revert in the unhappy case.

*There are 6 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

/// @audit expensive op on line 60
61:           require(_unstakeDelaySec > 0, "must specify unstake delay");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L61

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

/// @audit expensive op on line 36
37:           require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");

/// @audit expensive op on line 49
50:           require(paymasterId != address(0), "Paymaster Id can not be zero address");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L37

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit expensive op on line 168
169:          require(_owner != address(0),"Invalid owner");

/// @audit expensive op on line 168
170:          require(_entryPointAddress != address(0), "Invalid Entrypoint");

/// @audit expensive op on line 168
171:          require(_handler != address(0), "Invalid Entrypoint");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L169

### [G&#x2011;10]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 4 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

67:       function withdrawTo(address payable withdrawAddress, uint256 amount) public virtual onlyOwner {

99:       function withdrawStake(address payable withdrawAddress) external onlyOwner {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L67

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

449:      function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {

536:      function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 4 |  480 |
| [G&#x2011;02] | `<array>.length` should not be looked up in every loop of a `for`-loop | 1 |  3 |
| [G&#x2011;03] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 13 |  - |
| [G&#x2011;04] | Using `bool`s for storage incurs overhead | 1 |  17100 |
| [G&#x2011;05] | Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement | 3 |  18 |
| [G&#x2011;06] | `internal` functions not called by the contract should be removed to save deployment gas | 1 |  - |
| [G&#x2011;07] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 11 |  55 |
| [G&#x2011;08] | Using `private` rather than `public` for constants, saves gas | 4 |  - |
| [G&#x2011;09] | Division by two should use bit shifting | 1 |  20 |
| [G&#x2011;10] | Use custom errors rather than `revert()`/`require()` strings to save gas | 69 |  - |
| [G&#x2011;11] | Functions guaranteed to revert when called by normal users can be marked `payable` | 7 |  147 |

Total: 115 instances over 11 issues with **17823 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 4 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

/// @audit opInfo - (valid but excluded finding)
168:      function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L168

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

/// @audit data - (valid but excluded finding)
80        function execTransactionFromModuleReturnData(
81            address to,
82            uint256 value,
83            bytes memory data,
84            Enum.Operation operation
85:       ) public returns (bool success, bytes memory returnData) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L80-L85

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol

/// @audit transactions - (valid but excluded finding)
21:       function multiSend(bytes memory transactions) public payable {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L21

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol

/// @audit transactions - (valid but excluded finding)
26:       function multiSend(bytes memory transactions) public payable {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L26

### [G&#x2011;02]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit (valid but excluded finding)
468:          for (uint i = 0; i < dest.length;) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L468

### [G&#x2011;03]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 13 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol

/// @audit (valid but excluded finding)
27:           require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L27

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

/// @audit (valid but excluded finding)
36:           require(address(_entryPoint) != address(0), "VerifyingPaymaster: Entrypoint can not be zero address");

/// @audit (valid but excluded finding)
37:           require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");

/// @audit (valid but excluded finding)
49:           require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");

/// @audit (valid but excluded finding)
50:           require(paymasterId != address(0), "Paymaster Id can not be zero address");

/// @audit (valid but excluded finding)
66:           require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");

/// @audit (valid but excluded finding)
107:          require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");

/// @audit (valid but excluded finding)
108:          require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");

/// @audit (valid but excluded finding)
109:          require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L36

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

/// @audit (valid but excluded finding)
18:           require(_baseImpl != address(0), "base wallet address can not be zero");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L18

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit (valid but excluded finding)
77:           require(msg.sender == owner, "Smart Account:: Sender is not authorized");

/// @audit (valid but excluded finding)
110:          require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");

/// @audit (valid but excluded finding)
128:          require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L77

### [G&#x2011;04]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

/// @audit (valid but excluded finding)
15:       mapping (address => bool) public isAccountExist;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L15

### [G&#x2011;05]  Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement
This change saves **[6 gas](https://aws1.discourse-cdn.com/business6/uploads/zeppelin/original/2X/3/363a367d6d68851f27d2679d10706cd16d788b96.png)** per instance. The optimization works until solidity version [0.8.13](https://gist.github.com/IllIllI000/bf2c3120f24a69e489f12b3213c06c94) where there is a regression in gas costs.

*There are 3 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

/// @audit (valid but excluded finding)
61:           require(_unstakeDelaySec > 0, "must specify unstake delay");

/// @audit (valid but excluded finding)
64:           require(stake > 0, "no stake specified");

/// @audit (valid but excluded finding)
99:           require(stake > 0, "No stake to withdraw");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L61

### [G&#x2011;06]  `internal` functions not called by the contract should be removed to save deployment gas
If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol

/// @audit (valid but excluded finding)
20:       function _getImplementation() internal view returns (address _imp) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L20

### [G&#x2011;07]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 11 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

/// @audit (valid but excluded finding)
74:           for (uint256 i = 0; i < opslen; i++) {

/// @audit (valid but excluded finding)
80:           for (uint256 i = 0; i < opslen; i++) {

/// @audit (valid but excluded finding)
100:          for (uint256 i = 0; i < opasLen; i++) {

/// @audit (valid but excluded finding)
107:          for (uint256 a = 0; a < opasLen; a++) {

/// @audit (valid but excluded finding)
112:              for (uint256 i = 0; i < opslen; i++) {

/// @audit (valid but excluded finding)
114:                  opIndex++;

/// @audit (valid but excluded finding)
128:          for (uint256 a = 0; a < opasLen; a++) {

/// @audit (valid but excluded finding)
134:              for (uint256 i = 0; i < opslen; i++) {

/// @audit (valid but excluded finding)
136:                  opIndex++;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L74

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

/// @audit (valid but excluded finding)
124:              moduleCount++;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L124

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit (valid but excluded finding)
216:              nonces[batchId]++;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L216

### [G&#x2011;08]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 4 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol

/// @audit (valid but excluded finding)
12:       string public constant NAME = "Default Callback Handler";

/// @audit (valid but excluded finding)
13:       string public constant VERSION = "1.0.0";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L12

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

/// @audit (valid but excluded finding)
11:       string public constant VERSION = "1.0.2";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L11

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit (valid but excluded finding)
36:       string public constant VERSION = "1.0.2"; // using AA 0.3.0

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L36

### [G&#x2011;09]  Division by two should use bit shifting
`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

/// @audit (valid but excluded finding)
36:           return (a & b) + (a ^ b) / 2;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L36

### [G&#x2011;10]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 69 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

/// @audit (valid but excluded finding)
36:           require(beneficiary != address(0), "AA90 invalid beneficiary");

/// @audit (valid but excluded finding)
38:           require(success, "AA91 failed send to beneficiary");

/// @audit (valid but excluded finding)
170:          require(msg.sender == address(this), "AA92 internal call only");

/// @audit (valid but excluded finding)
213:              require(paymasterAndData.length >= 20, "AA93 invalid paymasterAndData");

/// @audit (valid but excluded finding)
353:          require(verificationGasLimit > gasUsedByValidateAccountPrepayment, "AA41 too little verificationGas");

/// @audit (valid but excluded finding)
397:          require(maxGasValues <= type(uint120).max, "AA94 gas values overflow");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L36

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

/// @audit (valid but excluded finding)
41:           require(newAmount <= type(uint112).max, "deposit overflow");

/// @audit (valid but excluded finding)
61:           require(_unstakeDelaySec > 0, "must specify unstake delay");

/// @audit (valid but excluded finding)
62:           require(_unstakeDelaySec >= info.unstakeDelaySec, "cannot decrease unstake time");

/// @audit (valid but excluded finding)
64:           require(stake > 0, "no stake specified");

/// @audit (valid but excluded finding)
65:           require(stake < type(uint112).max, "stake overflow");

/// @audit (valid but excluded finding)
82:           require(info.unstakeDelaySec != 0, "not staked");

/// @audit (valid but excluded finding)
83:           require(info.staked, "already unstaking");

/// @audit (valid but excluded finding)
99:           require(stake > 0, "No stake to withdraw");

/// @audit (valid but excluded finding)
100:          require(info.withdrawTime > 0, "must call unlockStake() first");

/// @audit (valid but excluded finding)
101:          require(info.withdrawTime <= block.timestamp, "Stake withdrawal is not due");

/// @audit (valid but excluded finding)
107:          require(success, "failed to withdraw stake");

/// @audit (valid but excluded finding)
117:          require(withdrawAmount <= info.deposit, "Withdraw amount too large");

/// @audit (valid but excluded finding)
121:          require(success, "failed to withdraw");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L41

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

/// @audit (valid but excluded finding)
21:           require(modules[SENTINEL_MODULES] == address(0), "BSA100");

/// @audit (valid but excluded finding)
25:               require(execute(to, 0, data, Enum.Operation.DelegateCall, gasleft()), "BSA000");

/// @audit (valid but excluded finding)
34:           require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

/// @audit (valid but excluded finding)
36:           require(modules[module] == address(0), "BSA102");

/// @audit (valid but excluded finding)
49:           require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

/// @audit (valid but excluded finding)
50:           require(modules[prevModule] == module, "BSA103");

/// @audit (valid but excluded finding)
68:           require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L21

```solidity
File: scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

/// @audit (valid but excluded finding)
74:           require(msg.sender == address(entryPoint()), "account: not from EntryPoint");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L74

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol

/// @audit (valid but excluded finding)
27:           require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L27

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

/// @audit (valid but excluded finding)
36:           require(address(_entryPoint) != address(0), "VerifyingPaymaster: Entrypoint can not be zero address");

/// @audit (valid but excluded finding)
37:           require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");

/// @audit (valid but excluded finding)
49:           require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");

/// @audit (valid but excluded finding)
50:           require(paymasterId != address(0), "Paymaster Id can not be zero address");

/// @audit (valid but excluded finding)
57:           require(amount <= currentBalance, "Insufficient amount to withdraw");

/// @audit (valid but excluded finding)
66:           require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");

/// @audit (valid but excluded finding)
107:          require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");

/// @audit (valid but excluded finding)
108:          require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");

/// @audit (valid but excluded finding)
109:          require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L36

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

/// @audit (valid but excluded finding)
18:           require(_baseImpl != address(0), "base wallet address can not be zero");

/// @audit (valid but excluded finding)
40:           require(address(proxy) != address(0), "Create2 call failed");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L18

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit (valid but excluded finding)
77:           require(msg.sender == owner, "Smart Account:: Sender is not authorized");

/// @audit (valid but excluded finding)
83:           require(msg.sender == owner || msg.sender == address(this),"Only owner or self");

/// @audit (valid but excluded finding)
89:           require(msg.sender == address(entryPoint()), "wallet: not from EntryPoint");

/// @audit (valid but excluded finding)
110:          require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");

/// @audit (valid but excluded finding)
121:          require(_implementation.isContract(), "INVALID_IMPLEMENTATION");

/// @audit (valid but excluded finding)
128:          require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");

/// @audit (valid but excluded finding)
167:          require(owner == address(0), "Already initialized");

/// @audit (valid but excluded finding)
168:          require(address(_entryPoint) == address(0), "Already initialized");

/// @audit (valid but excluded finding)
169:          require(_owner != address(0),"Invalid owner");

/// @audit (valid but excluded finding)
170:          require(_entryPointAddress != address(0), "Invalid Entrypoint");

/// @audit (valid but excluded finding)
171:          require(_handler != address(0), "Invalid Entrypoint");

/// @audit (valid but excluded finding)
224:          require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");

/// @audit (valid but excluded finding)
232:              require(success || _tx.targetTxGas != 0 || refundInfo.gasPrice != 0, "BSA013");

/// @audit (valid but excluded finding)
262:              require(success, "BSA011");

/// @audit (valid but excluded finding)
265:              require(transferToken(gasToken, receiver, payment), "BSA012");

/// @audit (valid but excluded finding)
286:              require(success, "BSA011");

/// @audit (valid but excluded finding)
289:              require(transferToken(gasToken, receiver, payment), "BSA012");

/// @audit (valid but excluded finding)
322:                  require(uint256(s) >= uint256(1) * 65, "BSA021");

/// @audit (valid but excluded finding)
325:                  require(uint256(s) + 32 <= signatures.length, "BSA022");

/// @audit (valid but excluded finding)
333:                  require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");

/// @audit (valid but excluded finding)
342:                  require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");

/// @audit (valid but excluded finding)
348:              require(_signer == owner, "INVALID_SIGNATURE");

/// @audit (valid but excluded finding)
351:              require(_signer == owner, "INVALID_SIGNATURE");

/// @audit (valid but excluded finding)
450:          require(dest != address(0), "this action will burn your funds");

/// @audit (valid but excluded finding)
452:          require(success,"transfer failed");

/// @audit (valid but excluded finding)
467:          require(dest.length == func.length, "wrong array lengths");

/// @audit (valid but excluded finding)
491:          require(success, "Userop Failed");

/// @audit (valid but excluded finding)
495:          require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");

/// @audit (valid but excluded finding)
502:          require(nonces[0]++ == userOp.nonce, "account: invalid nonce");

/// @audit (valid but excluded finding)
511:          require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L77

### [G&#x2011;11]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 7 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

/// @audit (valid but excluded finding)
24:       function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {

/// @audit (valid but excluded finding)
90:       function unlockStake() external onlyOwner {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

/// @audit (valid but excluded finding)
65:       function setSigner( address _newVerifyingSigner) external onlyOwner{

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit (valid but excluded finding)
455:      function pullTokens(address token, address dest, uint256 amount) external onlyOwner {

/// @audit (valid but excluded finding)
460:      function execute(address dest, uint value, bytes calldata func) external onlyOwner{

/// @audit (valid but excluded finding)
465:      function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{

/// @audit (valid but excluded finding)
489:      function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455


