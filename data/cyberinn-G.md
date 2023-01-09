# Gas Optimizations Report

## G-01 Splitting `require()` statements that use `&&` saves gas

### Impact

When using `&&` in a `require()` statement, the entire statement will be evaluated before the transaction reverts. If the first condition is false, the second condition will not be evaluated. This means that if the first condition is false, the second condition will not be evaluated, which is a waste of gas. Splitting the `require()` statement into two separate statements will save gas.

### Findings``

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

## G-02 Use named returns for local variables where it is possible.

### Impact

It is not necessary to have both a named return and a return statement.

### Findings

[scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L486](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L486)

```solidity
486:    uint256 maxFeePerGas = mUserOp.maxFeePerGas;
```

[scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L47](https://github.com/code-423n4/2023-01-biconomy/tree/main//scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L47)

```solidity
47:    uint256 maxFeePerGas = userOp.maxFeePerGas;
```
