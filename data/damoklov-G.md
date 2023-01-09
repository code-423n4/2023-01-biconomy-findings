## Biconomy Audit Report

## Gas Optimizations

### Increments should be unchecked

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

Also increments can be made pre-increment instead of post-increment, so `i++` would become  `++i` where necessary.

https://github.com/ethereum/solidity/issues/10695

Instances include:

```solidity
EntryPoint.handleAggregatedOps(): for (uint256 i = 0; i < opasLen; i++);
EntryPoint.handleAggregatedOps(): for (uint256 a = 0; a < opasLen; a++);
EntryPoint.handleAggregatedOps(): for (uint256 i = 0; i < opslen; i++);
```

The code would go from: 
```solidity
for (uint256 i; i < numIterations; i++) {  
 // ...  
}  
```

To: 
```solidity
for (uint256 i; i < numIterations;) {  
 // ...  
 unchecked { ++i; }  
}  
```

### An array's length should be cached to save gas in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

Instances include:

```solidity
SmartAccount.executeBatch(): for (uint i = 0; i < dest.length;);
```

The code would go from: 
```solidity
for (uint256 i; i < arr.length; ++i) {  
 // ...  
}  
```

To: 
```solidity
uint length = arr.length;
for (uint256 i; i < length; ++i) {  
 // ...  
}  
```

### Usage of calldata over memory when variable is read-only

Storing information inside `calldata` is less expensive than storing it in `memory`. In case you only need to read the variable, you should consider switching to `calldata`.

Instances include:

```solidity
SmartAccount.execTransaction(Transaction memory _tx, uint256 batchId, FeeRefund memory refundInfo, bytes memory signatures);
SmartAccount.checkSignatures(bytes32 dataHash, bytes memory data, bytes memory signatures);
SmartAccount.encodeTransactionData(Transaction memory _tx, FeeRefund memory refundInfo, uint256 _nonce);
```

The code would go from: 
```solidity
function example(string memory _data) {
 // ...  
}
```

To: 
```solidity
function example(string calldata _data) {
 // ...  
}
```


### Function ordering might be considered

There is additional value in renaming functions to be ordered based on the frequency you expect them to be used, saving average overall gas cost of interacting with your contract.

The function name might go from: 
```solidity
internalIncrementDeposit()
```

To: 
```solidity
internalIncrementDeposit_ikl() // 0x0000a970
```

Use this tool if needed: https://emn178.github.io/solidity-optimize-name/ 

### Loop merging should be applied
Solidity charges a lot of gas for loop usage, therefore loops should be merged in case they have common conditions.

Instances include:

```solidity
EntryPoint.handleOps(): for (uint256 i = 0; i < opslen; i++);
```

The code would go from: 
```solidity
for (uint256 i = 0; i < opslen; i++) {
	_validatePrepayment(i, ops[i], opInfos[i], address(0));
}

...

for (uint256 i = 0; i < opslen; i++) {
	collected += _executeUserOp(i, ops[i], opInfos[i]);
}
```

To: 
```solidity
for (uint256 i = 0; i < opslen; i++) {
	_validatePrepayment(i, ops[i], opInfos[i], address(0));
	collected += _executeUserOp(i, ops[i], opInfos[i]);
}
```

### No need to set default value to variables

If a variable is not set/initialized, the default value is assumed depending on data type, direct initialization of variables wastes gas.

Instances include:

```solidity
SmartAccount.execTransaction(): uint256 payment = 0;
SmartAccount.checkSignatures(): uint256 i = 0;
SmartAccount.executeBatch(): for (uint i = 0; i < dest.length;);

EntryPoint.handleOps(): for (uint256 i = 0; i < opslen; i++);
EntryPoint.handleOps(): uint256 collected = 0;
EntryPoint.handleOps(): for (uint256 i = 0; i < opslen; i++);
EntryPoint.handleAggregatedOps(): totalOps = 0;
EntryPoint.handleAggregatedOps(): for (uint256 i = 0; i < opasLen; i++);
EntryPoint.handleAggregatedOps(): uint256 opIndex = 0;
EntryPoint.handleAggregatedOps(): for (uint256 a = 0; a < opasLen; a++);
EntryPoint.handleAggregatedOps(): for (uint256 i = 0; i < opslen; i++);
EntryPoint.handleAggregatedOps(): uint256 collected = 0;
EntryPoint.handleAggregatedOps(): opIndex = 0;
EntryPoint.handleAggregatedOps(): for (uint256 a = 0; a < opasLen; a++);
EntryPoint.handleAggregatedOps(): for (uint256 i = 0; i < opslen; i++);
EntryPoint._validateAccountPrepayment(): uint256 missingAccountFunds = 0;

ModuleManager.getModulesPaginated(): uint256 moduleCount = 0;
```

The code would go from: 
```solidity
uint a = 0;
```

To: 
```solidity
uint a;
```


### Avoid compound assignment operator in state variables

Using compound assignment operators for state variables (like `state += x` or `state -= x`) is more expensive than using operator assignment (like `state = state + x` or `state = state - x`).

Instances include:

```solidity
EntryPoint.handleOps(): collected += _executeUserOp(i, ops[i], opInfos[i]);
EntryPoint.handleAggregatedOps(): totalOps += opsPerAggregator[i].userOps.length;
EntryPoint.handleAggregatedOps(): collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);
EntryPoint._handlePostOp(): actualGas += preGas - gasleft();
```

The code would go from:
```solidity
uint private _a;
function example() public {
	_a += 1;
}
```

To: 
```solidity
uint private _a;
function example() public {
	_a = _a + 1;
}
```
