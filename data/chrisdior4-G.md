# Gas Optimization Report

| G-O    |Issue|
|:------:|:----|
| [G&#x2011;01] | Use x != 0 instead of x > 0 for uint types | 2 |
| [G&#x2011;02] | Use custom errors instead of revert strings | 6 |
| [G&#x2011;03] | x = x - y costs less gas than x -= y, same for addition | 11 |
| [G&#x2011;04] | Remove public visibility from constant variables | 19 |
| [G&#x2011;05] | ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR-LOOP AND WHILE-LOOPS | 31 |
| [G&#x2011;06] | BYTES CONSTANTS ARE MORE EFFICIENT THAN STRING CONSTANTS | 2 |
| [G&#x2011;07] | Using Solidity version 0.8.17 will provide an overall gas optimization | 1 |
| [G&#x2011;08] | THERE’S NO NEED TO SET DEFAULT VALUES FOR VARIABLES | 1 |
| [G&#x2011;09] | USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS -3 | 1 |
| [G&#x2011;10] | ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS (--I/I-- TOO) | 1 |




Total: 10 issues

## Gas Optimization

### [G-01] Use custom errors instead of revert strings

Solidity 0.8.4 added the custom errors functionality, which can be use instead of revert strings, resulting in big gas savings on errors. Replace all revert statements with custom error ones

Example from `EntryPoint.sol`:

```solidity
require(beneficiary != address(0), "AA90 invalid beneficiary");
```

Line of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L36

### [G-02] Use x != 0 instead of x > 0 for uint types

The != operator costs less gas than > and for uint types you can use it to check for non-zero values to save gas

File: `SmartAccount.sol`

```solidity
 if (refundInfo.gasPrice > 0) {
```
Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L236

### [G-03] x = x - y costs less gas than x -= y, same for addition

You can replace all -= and += occurrences to save gas

File: `EntryPoint.sol`

```solidity
collected += _executeUserOp(i, ops[i], opInfos[i]);
```

```solidity
totalOps += opsPerAggregator[i].userOps.length;
```

```solidity
collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);
```

```solidity
actualGas += preGas - gasleft();
```

Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L81

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L101

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L135

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L468

### [G-04] Remove public visibility from constant variables

Constant variables are custom to the contract and won't need to be read on-chain - anyone can just see their values from the source code and, if needed, hardcode them into other contracts. Removing the public visibility will optimise deployment cost since no automatically generated getters will exist in the bytecode of the contract.


File: `SmartAccountFactory.sol`

```solidity
string public constant VERSION = "1.0.2";
```

Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L11

File: `DefaultCallbackHandler.sol`

```solidity
string public constant NAME = "Default Callback Handler";
string public constant VERSION = "1.0.0";
```

Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L12
- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L13

### [G-05] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR-LOOP AND WHILE-LOOPS

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

File: `EntryPoint.sol`

The for loops in function `handleAggregatedOps` can be unchecked.

### [G-06] BYTES CONSTANTS ARE MORE EFFICIENT THAN STRING CONSTANTS

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity. 
 
File: `SmartAccountFactory.sol`

```solidity
string public constant VERSION = "1.0.2";
```

Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L11

File: `DefaultCallbackHandler.sol`

```solidity
string public constant NAME = "Default Callback Handler";
string public constant VERSION = "1.0.0";
```

Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L12
- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L13

### [G-07] Using Solidity version 0.8.17 will provide an overall gas optimization

Using at least 0.8.10 will save gas due to skipped extcodesize check if there is a return value. Currently the contracts are compiled using version 0.8.7 (Foundry default). It is easily changeable to 0.8.17 using the command sed -i 's/0\.8\.7/^0.8.0/' test/*.sol && sed -i '4isolc = "0.8.17"' foundry.toml. This will have the following total savings obtained by forge snapshot --diff | tail -1:

### [G-08] THERE’S NO NEED TO SET DEFAULT VALUES FOR VARIABLES

If a variable is not set/initialized, the default value is assumed (0, false, 0x0 … depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

File: `EntryPoint.sol`

```solidity
uint256 missingAccountFunds = 0;
```
Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L313

###  [G-09]  USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS -3

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct.

File: `EntryPoint.sol`

```solidity
MemoryUserOp memory mUserOp = opInfo.mUserOp;
```

Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L171

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L293

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L351

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L444


### [G‑10] ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS (--I/I-- TOO)

Saves 5 gas per loop

All for loops in `EntryPoint.sol`.

Example: 

```solidity
for (uint256 i = 0; i < opslen; i++) {
```
