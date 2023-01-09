## 1. Splitting `require` statements that use `&&` saves gas

Instead of using the `&&` operator to check multiple conditions, use multiple `require()` statements. This will save approximately 3 gas per `&&`.

Here is an example of this issue:

File: `ModuleManager.sol` [Line 34](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34)

```solidity
        require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
```

In the example above, the `&&` could be split up:

```solidity
        require(module != address(0), "BSA101");
        require(module != SENTINEL_MODULES, "BSA101");
```

Here are 2 other instances of this issue:

- File: `ModuleManager.sol` [Line 49](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49)
- File: `ModuleManager.sol` [Line 68](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68)

## 2. Strict equalities cost less gas than non-strict

Strict equalities (`<`, `>`) cost less gas than non-strict (`<=`, `>=`). This is because strict equality uses fewer opcodes.

For instance:

File: `EntryPoint.sol` [Line 213](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L213)

```solidity
            require(paymasterAndData.length >= 20, "AA93 invalid paymasterAndData");
```

The `require` statement above could use `>` instead of `>=` to save gas:

```solidity
            require(paymasterAndData.length > 19, "AA93 invalid paymasterAndData");
```

Here are some more instances of this issue:

- File: `StakeManager.sol` [Line 41](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L41)
- File: `StakeManager.sol` [Line 62](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L62)
- File: `StakeManager.sol` [Line 117](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L117)
- File: `EntryPoint.sol` [Line 237](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L237)
- File: `EntryPoint.sol` [Line 397](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L397)

## 3. Functions with access control marked as `payable` cost less gas

Functions with access control will cost less gas when marked as `payable` (assuming the caller has correct permissions). This is because the compiler doesn't need to check for `msg.value`, which saves approximately **20 gas** per call.

Consider adding `payable` to the following functions to save gas:

- File: `SmartAccount.sol` [Line 449](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449)
- File: `SmartAccount.sol` [Line 455](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455)
- File: `SmartAccount.sol` [Line 460](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460)
- File: `SmartAccount.sol` [Line 465](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465)
- File: `SmartAccount.sol` [Line 536](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L536)

## 4. Useless `if` statement

File: `SmartAccount.sol` [Line 166 - 176](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166-L176)

```solidity
166    function init(address _owner, address _entryPointAddress, address _handler) public override initializer {
167        require(owner == address(0), "Already initialized");
168        require(address(_entryPoint) == address(0), "Already initialized");
169        require(_owner != address(0),"Invalid owner");
170        require(_entryPointAddress != address(0), "Invalid Entrypoint");
171        require(_handler != address(0), "Invalid Entrypoint");
172        owner = _owner;
173        _entryPoint =  IEntryPoint(payable(_entryPointAddress));
174        if (_handler != address(0)) internalSetFallbackHandler(_handler);
175        setupModules(address(0), bytes(""));
176    }
```

In the function above, the `if` statement ([line 174](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L174)) is checking the same thing as [line 171](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171), rendering it redundant.

As such, line 174 may be refactored to omit the `if` statement:

```
174        internalSetFallbackHandler(_handler);
```

## 5. `x += y` costs more gas than `x = x + y` (same for `-=`)

Using the addition operator instead of plus equals saves around **22 gas**

Here are some instances of this issue:

- File: `EntryPoint.sol` [Line 81](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L81)
- File: `EntryPoint.sol` [Line 101](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L101)
- File: `EntryPoint.sol` [Line 135](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L135)
- File: `EntryPoint.sol` [Line 468](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L468)

## 6. Unchecked maths

Use the `unchecked` keyword to avoid unnecessary arithmetic checks and save gas when an underflow/overflow will not happen.

For instance:

File: `EntryPoint.sol` [Line 100-102](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100-L102)

```solidity
        for (uint256 i = 0; i < opasLen; i++) {
            totalOps += opsPerAggregator[i].userOps.length;
        }
```

Since `i` is constrained by `opasLen` (a `uint256`), overflowing will never happen here.

As such, `i++` may be unchecked to save gas:

```solidity
        for (uint256 i = 0; i < opasLen;) {
            totalOps += opsPerAggregator[i].userOps.length;
            unchecked { i++; }
        }
```

## 7. Unnecessary cast into a `uint256()`

Casting a number into a `uint256()` is unnecessary if the number is already a `uint256`.

File: `SmartAccount.sol` [Line 322](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L322)

```solidity
                require(uint256(s) >= uint256(1) * 65, "BSA021");
```

`uint256(1)` is redundant as `1` is already a `uint256`. Moreover, `1 * 65` is equal to `65` so `1` may be omitted altogether:

```solidity
                require(uint256(s) >= 65, "BSA021");
```

## 8. Using `storage` instead of `memory` for structs/arrays saves gas

When retrieving data from a `storage` location, assigning the data to a `memory` variable will cause all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declaring the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

File: `EntryPoint.sol` ([Line 179](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L179), [Line 229](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L229), [Line 234](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L234), [Line 235](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L235))

```solidity
171:        MemoryUserOp memory mUserOp = opInfo.mUserOp;

229:        UserOpInfo memory outOpInfo;

234:        StakeInfo memory paymasterInfo = getStakeInfo(outOpInfo.mUserOp.paymaster);
235:        StakeInfo memory senderInfo = getStakeInfo(outOpInfo.mUserOp.sender);
```
