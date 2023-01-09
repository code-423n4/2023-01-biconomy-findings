# Gas Optimizations Report

|         | Issue                                                                                                                              | Instances |
| ------- | :--------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-001] | x += y or x -= y costs more gas than x = x + y or x = x - y for state variables                                                    |    22     |
| [G-002] | Splitting require() statements that use `&&` saves gas                                                                             |     3     |
| [G-003] | `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` |    33     |
| [G-004] | Functions guaranteed to revert when called by normal users can be marked payable                                                   |    10     |
| [G-005] | internal functions only called once can be inlined to save gas                                                                     |     9     |
| [G-006] | Use named returns for local variables where it is possible.                                                                        |    17     |

## [G-001] x += y or x -= y costs more gas than x = x + y or x = x - y for state variables

### Impact

Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

### Findings

Total:22

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L222](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L222)

```solidity
222:    result += 16;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L271](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L271)

```solidity
271:    result += 16;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L314](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L314)

```solidity
314:    result += 16;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L230](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L230)

```solidity
230:    result += 4;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L279](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L279)

```solidity
279:    result += 4;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L322](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L322)

```solidity
322:    result += 4;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L210](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L210)

```solidity
210:    result += 128;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L148](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L148)

```solidity
148:    result += 1;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L237](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L237)

```solidity
237:    result += 1;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L286](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L286)

```solidity
286:    result += 1;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L329](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L329)

```solidity
329:    result += 1;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L218](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L218)

```solidity
218:    result += 32;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L267](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L267)

```solidity
267:    result += 32;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L214](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L214)

```solidity
214:    result += 64;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L263](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L263)

```solidity
263:    result += 64;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L226](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L226)

```solidity
226:    result += 8;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L275](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L275)

```solidity
275:    result += 8;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L318](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L318)

```solidity
318:    result += 8;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L234](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L234)

```solidity
234:    result += 2;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L283](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L283)

```solidity
283:    result += 2;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L326](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L326)

```solidity
326:    result += 2;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L468](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L468)

```solidity
468:    actualGas += preGas - gasleft();
```

### Recommendation

Same as description

## [G-002] Splitting require() statements that use `&&` saves gas

### Impact

When using `&&` in a `require()` statement, the entire statement will be evaluated before the transaction reverts. If the first condition is false, the second condition will not be evaluated. This means that if the first condition is false, the second condition will not be evaluated, which is a waste of gas. Splitting the `require()` statement into two separate statements will save gas.

### Findings

Total:3

[scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68)

```solidity
68:    require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```

[scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34)

```solidity
34:    require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
```

[scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49)

```solidity
49:    require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
```

## [G-003] `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping`

### Impact

It may not be obvious, but every time you copy a storage `struct/array/mapping` to a `memory` variable, you are literally copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the storage, which is much cheaper.

### Findings

Total:33

[scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L336](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L336)

```solidity
336:    bytes memory contractSignature;
```

[scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L478](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L478)

```solidity
478:    (bool success, bytes memory result) = target.call{value : value}(data);
```

[scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L120](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L120)

```solidity
120:    bytes memory signatures) public payable virtual returns (bool success);
```

[scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L35](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L35)

```solidity
35:    bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
```

[scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L54](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L54)

```solidity
54:    bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
```

[scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L69](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L69)

```solidity
69:    bytes memory code = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
```

[scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L17](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L17)

```solidity
17:    bytes memory data = abi.encodeWithSelector(0xa9059cbb, receiver, amount);
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L29](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L29)

```solidity
29:    external virtual override returns (bytes memory context, uint256 deadline);
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L36](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L36)

```solidity
36:    (address paymasterId, bytes memory signature) = abi.decode(paymasterAndData[20:], (address, bytes));
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L126](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L126)

```solidity
126:    PaymasterContext memory data = context.decodePaymasterContext();
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L102](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L102)

```solidity
102:    PaymasterData memory paymasterData = userOp.decodePaymasterData();
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L17](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L17)

```solidity
17:    bytes memory initCallData = initCode[20 :];
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L71](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L71)

```solidity
71:    UserOpInfo[] memory opInfos = new UserOpInfo[](opslen);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L389](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L389)

```solidity
389:    MemoryUserOp memory mUserOp = outOpInfo.mUserOp;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L238](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L238)

```solidity
238:    StakeInfo memory factoryInfo = getStakeInfo(factory);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L104](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L104)

```solidity
104:    UserOpInfo[] memory opInfos = new UserOpInfo[](totalOps);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L229](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L229)

```solidity
229:    UserOpInfo memory outOpInfo;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L50](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L50)

```solidity
50:    bytes memory context = getMemoryBytesFromOffset(opInfo.contextOffset);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L235](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L235)

```solidity
235:    StakeInfo memory senderInfo = getStakeInfo(outOpInfo.mUserOp.sender);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L241](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L241)

```solidity
241:    AggregatorStakeInfo memory aggregatorInfo = AggregatorStakeInfo(aggregator, getStakeInfo(aggregator));
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L176](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L176)

```solidity
176:    (bool success,bytes memory result) = address(mUserOp.sender).call{gas : mUserOp.callGasLimit}(callData);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L171](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L171)

```solidity
171:    MemoryUserOp memory mUserOp = opInfo.mUserOp;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L293](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L293)

```solidity
293:    MemoryUserOp memory mUserOp = opInfo.mUserOp;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L351](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L351)

```solidity
351:    MemoryUserOp memory mUserOp = opInfo.mUserOp;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L444](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L444)

```solidity
444:    MemoryUserOp memory mUserOp = opInfo.mUserOp;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L234](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L234)

```solidity
234:    StakeInfo memory paymasterInfo = getStakeInfo(outOpInfo.mUserOp.paymaster);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L406](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L406)

```solidity
406:    bytes memory context;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L156](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L156)

```solidity
156:    function getSenderAddress(bytes memory initCode) external;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L27](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L27)

```solidity
27:    external returns (bytes memory context, uint256 deadline);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L26](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L26)

```solidity
26:    external view returns (bytes memory sigForUserOp);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L35](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L35)

```solidity
35:    function aggregateSignatures(UserOperation[] calldata userOps) external view returns (bytes memory aggregatesSignature);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L67](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L67)

```solidity
67:    function getDepositInfo(address account) external view returns (DepositInfo memory info);
```

[scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol#L19](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol#L19)

```solidity
19:    function isValidSignature(bytes memory _data, bytes memory _signature) public view virtual returns (bytes4);
```

## [G-004] Functions guaranteed to revert when called by normal users can be marked payable

### Impact

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are `CALLVALUE(2)`,`DUP1(3)`,`ISZERO(3)`,`PUSH2(3)`,`JUMPI(10)`,`PUSH1(3)`,`DUP1(3)`,`REVERT(0)`,`JUMPDEST(1)`,`POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Findings

Total:10

[scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455)

```solidity
455:    function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
```

[scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L536](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L536)

```solidity
536:    function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
```

[scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489)

```solidity
489:    function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {
```

[scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460)

```solidity
460:    function execute(address dest, uint value, bytes calldata func) external onlyOwner{
```

[scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465)

```solidity
465:    function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24)

```solidity
24:    function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90)

```solidity
90:    function unlockStake() external onlyOwner {
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99)

```solidity
99:    function withdrawStake(address payable withdrawAddress) external onlyOwner {
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L67](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L67)

```solidity
67:    function withdrawTo(address payable withdrawAddress, uint256 amount) public virtual onlyOwner {
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65)

```solidity
65:    function setSigner( address _newVerifyingSigner) external onlyOwner{
```

## [G-005] internal functions only called once can be inlined to save gas

### Impact

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

### Findings

Total:9

[scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L73](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L73)

```solidity
73:    function _requireFromEntryPoint() internal virtual view {
```

[scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L106](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L106)

```solidity
106:    function _payPrefund(uint256 missingAccountFunds) internal virtual {
```

[scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L96](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L96)

```solidity
96:    function _validateAndUpdateNonce(UserOperation calldata userOp) internal virtual;
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L48](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L48)

```solidity
48:    function _postOp(PostOpMode mode, bytes calldata context, uint256 actualGasCost) internal virtual {
```

[scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L104](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L104)

```solidity
104:    function _requireFromEntryPoint() internal virtual {
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L349](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L349)

```solidity
349:    function _validatePaymasterPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, uint256 requiredPreFund, uint256 gasUsedByValidateAccountPrepayment) internal returns (bytes memory context, uint256 deadline) {
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L203](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L203)

```solidity
203:    function _copyUserOpToMemory(UserOperation calldata userOp, MemoryUserOp memory mUserOp) internal pure {
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L248](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L248)

```solidity
248:    function _getRequiredPrefund(MemoryUserOp memory mUserOp) internal view returns (uint256 requiredPrefund) {
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L261](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L261)

```solidity
261:    function _createSenderIfNeeded(uint256 opIndex, UserOpInfo memory opInfo, bytes calldata initCode) internal {
```

## [G-006] Use named returns for local variables where it is possible.

### Impact

It is not necessary to have both a named return and a return statement.

### Findings

Total:17

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L146](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L146)

```solidity
146:    uint256 result = mulDiv(x, y, denominator);
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L173](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L173)

```solidity
173:    uint256 result = 1 << (log2(a) >> 1);
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L196](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L196)

```solidity
196:    uint256 result = sqrt(a);
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L206](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L206)

```solidity
206:    uint256 result = 0;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L259](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L259)

```solidity
259:    uint256 result = 0;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L310](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L310)

```solidity
310:    uint256 result = 0;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L249](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L249)

```solidity
249:    uint256 result = log2(value);
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L206](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L206)

```solidity
206:    uint256 result = 0;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L259](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L259)

```solidity
259:    uint256 result = 0;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L310](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L310)

```solidity
310:    uint256 result = 0;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L298](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L298)

```solidity
298:    uint256 result = log10(value);
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L206](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L206)

```solidity
206:    uint256 result = 0;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L259](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L259)

```solidity
259:    uint256 result = 0;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L310](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L310)

```solidity
310:    uint256 result = 0;
```

[scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L341](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L341)

```solidity
341:    uint256 result = log256(value);
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L486](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L486)

```solidity
486:    uint256 maxFeePerGas = mUserOp.maxFeePerGas;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L47](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L47)

```solidity
47:    uint256 maxFeePerGas = userOp.maxFeePerGas;
```
