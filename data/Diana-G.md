
## G-01 Increments can be unchecked

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used. It saves **30-40 gas** per loop.

_There are 5 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

100: for (uint256 i = 0; i < opasLen; i++) {
107: for (uint256 a = 0; a < opasLen; a++) {
112: for (uint256 i = 0; i < opslen; i++) {
128: for (uint256 a = 0; a < opasLen; a++) {
134: for (uint256 i = 0; i < opslen; i++) {
```

---------------

## G-02 x += y costs more gas than x = x + y for state variables (same applies for x -= y , x = x - y ; x \*=y ; x /= y)

Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**.

_There are 40 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

81: collected += _executeUserOp(i, ops[i], opInfos[i]);
101: totalOps += opsPerAggregator[i].userOps.length;
135: collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);
468: actualGas += preGas - gasleft();
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

```
File: contracts/smart-contract-wallet/libs/Math.sol

121: inverse *= 2 - denominator * inverse;
122: inverse *= 2 - denominator * inverse;
123: inverse *= 2 - denominator * inverse;
124: inverse *= 2 - denominator * inverse;
125: inverse *= 2 - denominator * inverse;
126: inverse *= 2 - denominator * inverse;
148: result += 1;
210: result += 128;
214: result += 64;
218: result += 32;
222: result += 16;
226: result += 8;
230: result += 4;
234: result += 2;
237: result += 1;
262: value /= 10**64;
263: result += 64;
266: value /= 10**32;
267: result += 32;
270: value /= 10**16;
271: result += 16;
274: value /= 10**8;
275: result += 8;
278: value /= 10**4;
279: result += 4;
282: value /= 10**2;
283: result += 2;
286: result += 1;
314: result += 16;
318: result += 8;
322: result += 4;
326: result += 2;
329: result += 1;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

```
File: contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

51: paymasterIdBalances[paymasterId] += msg.value;
58: paymasterIdBalances[msg.sender] -= amount;
128: paymasterIdBalances[extractedPaymasterId] -= actualGasCost;
```

-----

## G-03 Splitting require() statements that use && saves gas

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas

_There are 3 instance of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

```
File: contracts/smart-contract-wallet/base/ModuleManager.sol

34: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
49: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
68: require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104")
```

---------

## G-04 Internal functions only called once can be inlined to save gas

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

_There are 27 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

```
File: contracts/smart-contract-wallet/BaseSmartAccount.sol

73: function _requireFromEntryPoint() internal virtual view {
96: function _validateAndUpdateNonce(UserOperation calldata userOp) internal virtual;
106: function _payPrefund(uint256 missingAccountFunds) internal virtual {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```
File: contracts/smart-contract-wallet/SmartAccount.sol

181: function max(uint256 a, uint256 b) internal pure returns (uint256) {
506: function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

203: function _copyUserOpToMemory(UserOperation calldata userOp, MemoryUserOp memory mUserOp) internal pure {
248: function _getRequiredPrefund(MemoryUserOp memory mUserOp) internal view returns (uint256 requiredPrefund) {
261: function _createSenderIfNeeded(uint256 opIndex, UserOpInfo memory opInfo, bytes calldata initCode) internal {
289: function _validateAccountPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, address aggregator, uint256 requiredPrefund)
349: function _validatePaymasterPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, uint256 requiredPreFund, uint256 gasUsedByValidateAccountPrepayment) internal returns (bytes memory context, uint256 deadline) {
496: function min(uint256 a, uint256 b) internal pure returns (uint256) {
500: function getOffsetOfMemoryBytes(bytes memory data) internal pure returns (uint256 offset) {
504: function getMemoryBytesFromOffset(uint256 offset) internal pure returns (bytes memory data) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

```
File: contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

38: function internalIncrementDeposit(address account, uint256 amount) internal {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol

```
File: contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol

57: function pack(UserOperation calldata userOp) internal pure returns (bytes memory ret) {
77: function min(uint256 a, uint256 b) internal pure returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol

```
File: contracts/smart-contract-wallet/aa-4337/utils/Exec.sol

19: function staticcall(
40: function getReturnData() internal pure returns (bytes memory returnData) {
51: function revertWithData(bytes memory returnData) internal pure {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

```
File: contracts/smart-contract-wallet/base/ModuleManager.sol

20: function setupModules(address to, bytes memory data) internal {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol

```
File: contracts/smart-contract-wallet/common/SignatureDecoder.sol

10: function signatureSplit(bytes memory signatures, uint256 pos)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol

```
File: contracts/smart-contract-wallet/common/Singleton.sol

12: function _setImplementation(address _imp) internal {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

```
File: contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

48: function _postOp(PostOpMode mode, bytes calldata context, uint256 actualGasCost) internal virtual {
104: function _requireFromEntryPoint() internal virtual {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

```
File: contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

24: function paymasterContext(
34: function decodePaymasterData(UserOperation calldata op) internal pure returns (PaymasterData memory) {
43: function decodePaymasterContext(bytes memory context) internal pure returns (PaymasterContext memory) {
```

-----------

## G-05 Not using the named return variables when a functions returns,wastes deployment gas

_There are 8 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```
File: contracts/smart-contract-wallet/SmartAccount.sol

506: function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

168: function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

```
File: contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

18: function getDepositInfo(address account) public view returns (DepositInfo memory info) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

```
File: contracts/smart-contract-wallet/libs/Math.sol

55: function mulDiv(
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

```
File: contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

24: function paymasterContext(
34: function decodePaymasterData(UserOperation calldata op) internal pure returns (PaymasterData memory) {
43: function decodePaymasterContext(bytes memory context) internal pure returns (PaymasterContext memory) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

```
File: contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

97: function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 /*userOpHash*/, uint256 requiredPreFund)
```

----------

## G-06 Require() or revert() strings longer than 32 bytes cost extra gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

I suggest shortening the revert strings to fit in 32 bytes, or using custom errors.

_There are 14 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```
File: contracts/smart-contract-wallet/SmartAccount.sol

77: require(msg.sender == owner, "Smart Account:: Sender is not authorized");
110: require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
128: require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

```
File: contracts/smart-contract-wallet/SmartAccountFactory.sol

18: require(_baseImpl != address(0), "base wallet address can not be zero");
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol

```
File: contracts/smart-contract-wallet/libs/MultiSend.sol

27: require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

```
File: contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

36: require(address(_entryPoint) != address(0), "VerifyingPaymaster: Entrypoint can not be zero address");
37: require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");
42: revert("Deposit must be for a paymasterId. Use depositFor");
49: require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");
50: require(paymasterId != address(0), "Paymaster Id can not be zero address");
66: require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
107: require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
108: require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");
109: require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");
```

---------

## G-07 Duplicated require() or revert() checks should be refactored to a modifier or function

_There are 2 instances of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

```
File: contracts/smart-contract-wallet/base/ModuleManager.sol

34: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
49: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
```

--------

## G-08 Multiplication or division by 2 shoudl use bit shifting

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas.

While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two.

_There is 1 instance of this issue_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

```
File: contracts/smart-contract-wallet/libs/Math.sol

36: return (a & b) + (a ^ b) / 2;
```

-------
