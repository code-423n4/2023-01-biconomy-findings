## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `abi.encode()` is less efficient than `abi.encodepacked()` | 5 | 500 |
| [GAS&#x2011;2](#GAS&#x2011;2) | State variables only set in the `constructor` should be declared immutable | 1 | - |
| [GAS&#x2011;3](#GAS&#x2011;3) | Setting the `constructor` to `payable` | 6 | 78 |
| [GAS&#x2011;4](#GAS&#x2011;4) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 8 | 532 |
| [GAS&#x2011;5](#GAS&#x2011;5) | Empty Blocks Should Be Removed Or Emit Something | 3 | - |
| [GAS&#x2011;6](#GAS&#x2011;6) | Using fixed bytes is cheaper than using `string` | 4 | - |
| [GAS&#x2011;7](#GAS&#x2011;7) | Functions guaranteed to revert when called by normal users can be marked `payable` | 18 | 378 |
| [GAS&#x2011;8](#GAS&#x2011;8) | `internal` functions only called once can be inlined to save gas | 44 | - |
| [GAS&#x2011;9](#GAS&#x2011;9) | Multiplication/division By Two Should Use Bit Shifting | 3 | - |
| [GAS&#x2011;10](#GAS&#x2011;10) | Optimize names to save gas | 20 | 440 |
| [GAS&#x2011;11](#GAS&#x2011;11) | `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 6 | - |
| [GAS&#x2011;12](#GAS&#x2011;12) | Public Functions To External | 45 | - |
| [GAS&#x2011;13](#GAS&#x2011;13) | `require()` Should Be Used Instead Of `assert()` | 1 | - |
| [GAS&#x2011;14](#GAS&#x2011;14) | `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas | 19 | - |
| [GAS&#x2011;15](#GAS&#x2011;15) | Splitting `require()` Statements That Use `&&` Saves Gas | 3 | 27 |
| [GAS&#x2011;16](#GAS&#x2011;16) | Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It | 2 | - |
| [GAS&#x2011;17](#GAS&#x2011;17) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 11 | - |
| [GAS&#x2011;18](#GAS&#x2011;18) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 5 | 175 |
| [GAS&#x2011;19](#GAS&#x2011;19) | Using `unchecked` blocks to save gas | 18 | 7344 |
| [GAS&#x2011;20](#GAS&#x2011;20) | Use solidity version 0.8.17 to gain some gas boost | 37 | 3256 |
| [GAS&#x2011;21](#GAS&#x2011;21) | Using `storage` instead of `memory` saves gas | 1 | 350 |
| [GAS&#x2011;22](#GAS&#x2011;22) | Struct packing  | 3 | - |


Total: 263 contexts over 22 issues

## Gas Optimizations

### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
136: return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L136

```solidity
431: abi.encode(
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L431

```solidity
197: return keccak256(abi.encode(userOp.hash(), address(this), block.chainid));
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L197

```solidity
28: return abi.encode(data.paymasterId);
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol#L28

```solidity
80: return keccak256(abi.encode(
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L80






### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> State variables only set in the `constructor` should be declared immutable

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

#### <ins>Proof Of Concept</ins>

```solidity
19: _defaultImpl = _baseImpl
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccountFactory.sol#L19





### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
15: constructor(address _implementation)
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\Proxy.sol#L15

```solidity
17: constructor(address _baseImpl)
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccountFactory.sol#L17

```solidity
20: constructor(IEntryPoint _entryPoint)
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L20

```solidity
12: constructor()
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\MultiSend.sol#L12

```solidity
20: constructor(IEntryPoint _entryPoint)
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L20

```solidity
35: constructor(IEntryPoint _entryPoint, address _verifyingSigner) BasePaymaster(_entryPoint)
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L35




#### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
262: require(success, "BSA011");
286: require(success, "BSA011");
265: require(transferToken(gasToken, receiver, payment), "BSA012");
289: require(transferToken(gasToken, receiver, payment), "BSA012");
348: require(_signer == owner, "INVALID_SIGNATURE");
351: require(_signer == owner, "INVALID_SIGNATURE");

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L262

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L286

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L265

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L289

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L348

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L351



```solidity
34: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
49: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L34

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L49








### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```solidity
550: receive() external payable {}
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L550

```solidity
119: try aggregator.validateSignatures(ops, opa.signature) {}
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L119

```solidity
458: try IPaymaster(paymaster).postOp{gas : mUserOp.verificationGasLimit}(mode, context, actualGasCost) {}
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L458






### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Using fixed bytes is cheaper than using `string`

As a rule of thumb, use `bytes` for arbitrary-length raw byte data and string for arbitrary-length `string` (UTF-8) data. If you can limit the length to a certain number of bytes, always use one of `bytes1` to `bytes32` because they are much cheaper.

#### <ins>Proof Of Concept</ins>


```solidity
36: string public constant VERSION = "1.0.2"; // using AA 0.3.0
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L36

```solidity
11: string public constant VERSION = "1.0.2";
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccountFactory.sol#L11

```solidity
12: string public constant NAME = "Default Callback Handler";
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol#L12

```solidity
13: string public constant VERSION = "1.0.0";
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol#L13






### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier or require such as onlyOwner/onlyX is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

#### <ins>Proof Of Concept</ins>

```solidity
449: function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L449

```solidity
455: function pullTokens(address token, address dest, uint256 amount) external onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L455

```solidity
460: function execute(address dest, uint value, bytes calldata func) external onlyOwner{
465: function execute(address dest, uint value, bytes calldata func) external onlyOwner{

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L460

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L465



```solidity
465: function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L465

```solidity
489: function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L489

```solidity
536: function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L536

```solidity
24: function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L24

```solidity
67: function withdrawTo(address payable withdrawAddress, uint256 amount) public onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L67

```solidity
75: function addStake(uint32 unstakeDelaySec) external payable onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L75

```solidity
90: function unlockStake() external onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L90

```solidity
99: function withdrawStake(address payable withdrawAddress) external onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L99

```solidity
24: function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L24

```solidity
67: function withdrawTo(address payable withdrawAddress, uint256 amount) public virtual onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L67

```solidity
75: function addStake(uint32 unstakeDelaySec) external payable onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L75

```solidity
90: function unlockStake() external onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L90

```solidity
99: function withdrawStake(address payable withdrawAddress) external onlyOwner {

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L99

```solidity
65: function setSigner( address _newVerifyingSigner) external onlyOwner{

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L65



#### <ins>Recommended Mitigation Steps</ins>
Functions guaranteed to revert when called by normal users can be marked payable.




### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
73: function _requireFromEntryPoint

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\BaseSmartAccount.sol#L73

```solidity
87: function _validateSignature

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\BaseSmartAccount.sol#L87

```solidity
181: function max

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L181

```solidity
501: function _validateAndUpdateNonce

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L501

```solidity
506: function _validateSignature

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L506

```solidity
48: function _postOp

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L48

```solidity
104: function _requireFromEntryPoint

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L104

```solidity
203: function _copyUserOpToMemory

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L203

```solidity
248: function _getRequiredPrefund

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L248

```solidity
261: function _createSenderIfNeeded

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L261

```solidity
289: function _validateAccountPrepayment

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L289

```solidity
349: function _validatePaymasterPrepayment

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L349

```solidity
496: function min

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L496

```solidity
500: function getOffsetOfMemoryBytes

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L500

```solidity
504: function getMemoryBytesFromOffset

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L504

```solidity
23: function getStakeInfo

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L23

```solidity
38: function internalIncrementDeposit

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L38

```solidity
36: function getSender

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\interfaces\UserOperation.sol#L36

```solidity
45: function gasPrice

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\interfaces\UserOperation.sol#L45

```solidity
57: function pack

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\interfaces\UserOperation.sol#L57

```solidity
73: function hash

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\interfaces\UserOperation.sol#L73

```solidity
77: function min

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\interfaces\UserOperation.sol#L77

```solidity
19: function staticcall

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\utils\Exec.sol#L19

```solidity
29: function delegateCall

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\utils\Exec.sol#L29

```solidity
40: function getReturnData

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\utils\Exec.sol#L40

```solidity
51: function revertWithData

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\utils\Exec.sol#L51

```solidity
57: function callAndRevert

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\utils\Exec.sol#L57

```solidity
13: function execute

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\Executor.sol#L13

```solidity
14: function internalSetFallbackHandler

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\FallbackManager.sol#L14

```solidity
20: function setupModules

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L20

```solidity
10: function transferToken

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\common\SecuredTokenTransfer.sol#L10

```solidity
12: function _setImplementation

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\common\Singleton.sol#L12

```solidity
20: function _getImplementation

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\common\Singleton.sol#L20

```solidity
11: function isContract

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\LibAddress.sol#L11

```solidity
19: function max

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\Math.sol#L19

```solidity
26: function min

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\Math.sol#L26

```solidity
34: function average

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\Math.sol#L34

```solidity
45: function ceilDiv

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\Math.sol#L45

```solidity
48: function _postOp

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L48

```solidity
104: function _requireFromEntryPoint

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L104

```solidity
24: function paymasterContext

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol#L24

```solidity
34: function decodePaymasterData

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol#L34

```solidity
43: function decodePaymasterContext

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol#L43

```solidity
119: function _postOp

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L119







### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Multiplication/division By Two Should Use Bit Shifting

<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

#### <ins>Proof Of Concept</ins>


```solidity
36: return (a & b) + (a ^ b) / 2;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\Math.sol#L36

```solidity
281: if (value >= 10**2) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\Math.sol#L281

```solidity
282: value /= 10**2;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\Math.sol#L282





### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

All in-scope contracts

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol



#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.



### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```solidity
81: collected += _executeUserOp(i, ops[i], opInfos[i]);

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L81

```solidity
101: totalOps += opsPerAggregator[i].userOps.length;
135: collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L101

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L135



```solidity
468: actualGas += preGas - gasleft();

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L468

```solidity
148: result += 1;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\Math.sol#L148


```solidity
51: paymasterIdBalances[paymasterId] += msg.value;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L51

```solidity
58: paymasterIdBalances[msg.sender] -= amount;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L58

```solidity
128: paymasterIdBalances[extractedPaymasterId] -= actualGasCost;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L128







### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```solidity
function nonce() public view virtual returns (uint256);
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\BaseSmartAccount.sol#L41

```solidity
function nonce() public view virtual override returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L93

```solidity
function nonce(uint256 _batchId) public view virtual override returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L93

```solidity
function entryPoint() public view virtual override returns (IEntryPoint) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L101

```solidity
function domainSeparator() public view returns (bytes32) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L135

```solidity
function getChainId() public view returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L140

```solidity
function getNonce(uint256 batchId)
    public view
    returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L155

```solidity
function init(address _owner, address _entryPointAddress, address _handler) public override initializer {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L166

```solidity
function execTransaction(
        Transaction memory _tx,
        uint256 batchId,
        FeeRefund memory refundInfo,
        bytes memory signatures
    ) public payable virtual override returns (bool success) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L192

```solidity
function checkSignatures(
        bytes32 dataHash,
        bytes memory data,
        bytes memory signatures
    ) public view virtual {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L302

```solidity
function getTransactionHash(
        address to,
        uint256 value,
        bytes calldata data,
        Enum.Operation operation,
        uint256 targetTxGas,
        uint256 baseGas,
        uint256 gasPrice,
        uint256 tokenGasPriceFactor,
        address gasToken,
        address payable refundReceiver,
        uint256 _nonce
    ) public view returns (bytes32) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L389

```solidity
function encodeTransactionData(
        Transaction memory _tx,
        FeeRefund memory refundInfo,
        uint256 _nonce
    ) public view returns (bytes memory) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L424

```solidity
function getDeposit() public view returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L518

```solidity
function addDeposit() public payable {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L525

```solidity
function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L536

```solidity
function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address proxy){
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccountFactory.sol#L33

```solidity
function deployWallet(address _owner, address _entryPoint, address _handler) public returns(address proxy){
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccountFactory.sol#L53

```solidity
function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L24

```solidity
function deposit() public payable {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L58

```solidity
function withdrawTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L67

```solidity
function getDeposit() public view returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\BasePaymaster.sol#L82

```solidity
function handleOps(UserOperation[] calldata ops, address payable beneficiary) public {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L68

```solidity
function handleAggregatedOps(
        UserOpsPerAggregator[] calldata opsPerAggregator,
        address payable beneficiary
    ) public {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L93

```solidity
function getUserOpHash(UserOperation calldata userOp) public view returns (bytes32) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L196

```solidity
function getSenderAddress(bytes calldata initCode) public {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L280

```solidity
function getDepositInfo(address account) public view returns (DepositInfo memory info) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L18

```solidity
function balanceOf(address account) public view returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L30

```solidity
function depositTo(address account) public payable {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L48

```solidity
function addStake(uint32 _unstakeDelaySec) public payable {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L59

```solidity
function setFallbackHandler(address handler) public authorized {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\FallbackManager.sol#L26

```solidity
function enableModule(address module) public authorized {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L32

```solidity
function disableModule(address prevModule, address module) public authorized {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L47

```solidity
function execTransactionFromModule(
        address to,
        uint256 value,
        bytes memory data,
        Enum.Operation operation
    ) public virtual returns (bool success) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L61

```solidity
function execTransactionFromModuleReturnData(
        address to,
        uint256 value,
        bytes memory data,
        Enum.Operation operation
    ) public returns (bool success, bytes memory returnData) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L80

```solidity
function isModuleEnabled(address module) public view returns (bool) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L105

```solidity
function multiSend(bytes memory transactions) public payable {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\MultiSend.sol#L26

```solidity
function multiSend(bytes memory transactions) public payable {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\MultiSendCallOnly.sol#L21

```solidity
function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L24

```solidity
function deposit() public virtual payable {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L58

```solidity
function withdrawTo(address payable withdrawAddress, uint256 amount) public virtual onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L67

```solidity
function getDeposit() public view returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\BasePaymaster.sol#L82

```solidity
function deposit() public virtual override payable {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L41

```solidity
function depositFor(address paymasterId) public payable {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L48

```solidity
function withdrawTo(address payable withdrawAddress, uint256 amount) public override {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L55

```solidity
function getHash(UserOperation calldata userOp)
    public pure returns (bytes32) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L77






### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> `require()` Should Be Used Instead Of `assert()`

#### <ins>Proof Of Concept</ins>


```solidity
13: assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\common\Singleton.sol#L13





### <a href="#Summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas

#### <ins>Proof Of Concept</ins>


```solidity
74: require(msg.sender == address(entryPoint()), "account: not from EntryPoint");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\BaseSmartAccount.sol#L74

```solidity
110: require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L110

```solidity
128: require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L128

```solidity
342: require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L342

```solidity
450: require(dest != address(0), "this action will burn your funds");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L450

```solidity
495: require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L495

```solidity
38: require(success, "AA91 failed send to beneficiary");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L38

```solidity
213: require(paymasterAndData.length >= 20, "AA93 invalid paymasterAndData");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L213

```solidity
353: require(verificationGasLimit > gasUsedByValidateAccountPrepayment, "AA41 too little verificationGas");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L353

```solidity
62: require(_unstakeDelaySec >= info.unstakeDelaySec, "cannot decrease unstake time");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L62

```solidity
100: require(info.withdrawTime > 0, "must call unlockStake() first");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L100

```solidity
27: require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\libs\MultiSend.sol#L27

```solidity
49: require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L49

```solidity
50: require(paymasterId != address(0), "Paymaster Id can not be zero address");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L50

```solidity
57: require(amount <= currentBalance, "Insufficient amount to withdraw");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L57

```solidity
66: require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L66

```solidity
107: require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L107

```solidity
108: require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L108

```solidity
109: require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L109





### <a href="#Summary">[GAS&#x2011;15]</a><a name="GAS&#x2011;15"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");` use:

```
	require(version == 1);
	require(_bytecodeHash[1] == bytes1(0));
```

#### <ins>Proof Of Concept</ins>

```solidity
34: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L34

```solidity
49: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L49

```solidity
68: require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\ModuleManager.sol#L68





### <a href="#Summary">[GAS&#x2011;16]</a><a name="GAS&#x2011;16"> Help The Optimizer By Saving A Storage Variable's Reference Instead Of Repeatedly Fetching It

To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

#### <ins>Proof Of Concept</ins>


```solidity
113: _validatePrepayment(opIndex, ops[i], opInfos[opIndex], address(aggregator));
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L113

```solidity
135: collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L135






### <a href="#Summary">[GAS&#x2011;17]</a><a name="GAS&#x2011;17"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```solidity
88: internal virtual returns (uint256 deadline);
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\BaseSmartAccount.sol#L88

```solidity
307: uint8 v;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L307

```solidity
474: internalIncrementDeposit(refundAddress, refund);
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L474

```solidity
49: internalIncrementDeposit(account, msg.value);
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L49

```solidity
84: uint64 withdrawTime = uint64(block.timestamp) + info.unstakeDelaySec;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L84

```solidity
54: uint112 deposit;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\interfaces\IStakeManager.sol#L54

```solidity
56: uint112 stake;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\interfaces\IStakeManager.sol#L56

```solidity
57: uint32 unstakeDelaySec;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\interfaces\IStakeManager.sol#L57

```solidity
58: uint64 withdrawTime;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\interfaces\IStakeManager.sol#L58

```solidity
27: internalSetFallbackHandler(handler);
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\base\FallbackManager.sol#L27

```solidity
59: interfaceId == type(IERC165).interfaceId;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol#L59






### <a href="#Summary">[GAS&#x2011;18]</a><a name="GAS&#x2011;18"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
100: for (uint256 i = 0; i < opasLen; i++) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L100

```solidity
107: for (uint256 a = 0; a < opasLen; a++) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L107

```solidity
112: for (uint256 i = 0; i < opslen; i++) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L112





### <a href="#Summary">[GAS&#x2011;19]</a><a name="GAS&#x2011;19"> Using `unchecked` blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isnâ€™t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block

#### <ins>Proof Of Concept</ins>

```solidity
239: payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L239

```solidity
267: uint256 requiredGas = startGas - gasleft();
291: uint256 requiredGas = startGas - gasleft();
372: uint256 requiredGas = startGas - gasleft();

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L267

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L291

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L372



```solidity
347: _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L347

```solidity
267: uint256 requiredGas = startGas - gasleft();
291: uint256 requiredGas = startGas - gasleft();
372: uint256 requiredGas = startGas - gasleft();

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L267

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L291

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L372



```solidity
55: uint256 actualGas = preGas - gasleft() + opInfo.preOpGas;
186: uint256 actualGas = preGas - gasleft() + opInfo.preOpGas;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L55

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L186



```solidity
317: missingAccountFunds = bal > requiredPrefund ? 0 : requiredPrefund - bal;
336: senderInfo.deposit = uint112(deposit - requiredPrefund);
338: gasUsedByValidateAccountPrepayment = preGas - gasleft();

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L317

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L336

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L338



```solidity
354: uint256 gas = verificationGasLimit - gasUsedByValidateAccountPrepayment;
362: paymasterInfo.deposit = uint112(deposit - requiredPreFund);

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L354

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L362



```solidity
418: uint256 gasUsed = preGas - gasleft();
425: outOpInfo.preOpGas = preGas - gasleft() + userOp.preVerificationGas;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L418

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L425



```solidity
118: info.deposit = uint112(info.deposit - withdrawAmount);

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol#L118






### <a href="#Summary">[GAS&#x2011;20]</a><a name="GAS&#x2011;20"> Use solidity version 0.8.17 to gain some gas boost

Upgrade to the latest solidity version 0.8.17 to get additional gas savings.

#### <ins>Proof Of Concept</ins>

All in-scope contracts





### <a href="#Summary">[GAS&#x2011;21]</a><a name="GAS&#x2011;21"> Using `storage` instead of `memory` saves gas

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another `memory` array/struct

#### <ins>Proof Of Concept</ins>


```solidity
17: bytes memory initCallData = initCode[20 :];

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\aa-4337\core\SenderCreator.sol#L17





### <a href="#Summary">[GAS&#x2011;22]</a><a name="GAS&#x2011;22"> Struct packing

Entrypoint.sol

```solidity  
  145:     struct MemoryUserOp {
  146:         address sender;
  147:         uint256 nonce;
  148:         uint256 callGasLimit;
  149:         uint256 verificationGasLimit;
  150:         uint256 preVerificationGas;
  151:         address paymaster;
  152:         uint256 maxFeePerGas;
  153:         uint256 maxPriorityFeePerGas;
  154:     }
```

Nonce unlikely to reach max uint256. Can set to uint96 and thus saving 1 storage slot.

```solidity  
  145:     struct MemoryUserOp {
  146:         address sender;
  147:         uint96 nonce;
  148:         uint256 callGasLimit;
  149:         uint256 verificationGasLimit;
  150:         uint256 preVerificationGas;
  151:         address paymaster;
  152:         uint256 maxFeePerGas;
  153:         uint256 maxPriorityFeePerGas;
  154:     }
```

IStakeManager.sol

```solidity
   62:     struct StakeInfo {
   63:         uint256 stake;
   64:         uint256 unstakeDelaySec;
   65:     }
```

`stake` is seen to be used in `struct DepositInfo` with `uint112` and `unstakeDelaySec` with `uint32`.
Hence, we can save 1 storage slot by changing to:

```solidity
   62:     struct StakeInfo {
   63:         uint112 stake;
   64:         uint32 unstakeDelaySec;
   65:     }
```

UserOperation.sol

```solidity
   19:     struct UserOperation {
   20: 
   21:         address sender;
   22:         uint256 nonce;
   23:         bytes initCode;
   24:         bytes callData;
   25:         uint256 callGasLimit;
   26:         uint256 verificationGasLimit;
   27:         uint256 preVerificationGas;
   28:         uint256 maxFeePerGas;
   29:         uint256 maxPriorityFeePerGas;
   30:         bytes paymasterAndData;
   31:         bytes signature;
   32:     }
```

Nonce unlikely to reach max uint256. Can set to uint96 and thus saving 1 storage slot.

```solidity
   19:     struct UserOperation {
   20: 
   21:         address sender;
   22:         uint96 nonce;
   23:         bytes initCode;
   24:         bytes callData;
   25:         uint256 callGasLimit;
   26:         uint256 verificationGasLimit;
   27:         uint256 preVerificationGas;
   28:         uint256 maxFeePerGas;
   29:         uint256 maxPriorityFeePerGas;
   30:         bytes paymasterAndData;
   31:         bytes signature;
   32:     }
```

