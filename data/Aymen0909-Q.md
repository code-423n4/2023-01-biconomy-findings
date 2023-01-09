
# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1   | Redundant if check in the `init` function can be removed | NC | 1 |
| 2   | Named return variables not used anywhere in the functions | NC | 2 |
| 3   | Non-assembly method available | NC |  1 |


## Findings

### 1- Redundant if check in the `init` function can be removed :

### Risk : NON CRITICAL

In the `init` function from the `SmartAccount` the following check is redundant :

File: SmartAccount.sol [Line 174](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L174)
```
if (_handler != address(0)) internalSetFallbackHandler(_handler);
```

Because the check [line 171](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171) ensures that we always have `_handler != address(0)` and so the `internalSetFallbackHandler` should be called immediatly and the if statement should be removed.


### 2- Named return variables not used anywhere in the function :

When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

### Risk : NON CRITICAL

### Proof of Concept
Instances include:

File: aa-4337/core/StakeManager.sol [Line 18](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L18)
```
returns (DepositInfo memory info)
```

### Mitigation

Either use the named return variables inplace of the return statement or remove them.

### 3- Non-assembly method available :

`<address>.code.length` can be used in Solidity `>= 0.8.0` to access an account’s code size and check if it is a contract without inline assembly.

### Risk : NON CRITICAL

### Proof of Concept

There is 1 instance of this issue :

File: libs/LibAddress.sol [Line 11-16](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol#L11-L16)

```
function isContract(address account) internal view returns (bool) {
    uint256 csize;
    // solhint-disable-next-line no-inline-assembly
    assembly { csize := extcodesize(account) }
    return csize != 0;
}
```

### Mitigation
Assembly method can be replaced it with : 

```
function isContract(address account) internal view returns (bool) {
    return account.code.length != 0;
}
```
