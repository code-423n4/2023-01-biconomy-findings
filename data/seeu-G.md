## Missing implementation Shift Right/Left for division and multiplication

### Description

The `SHR` opcode only utilizes 3 gas, compared to the 5 gas used by the `DIV` opcode. Additionally, shifting is used to get around Solidity's division operation's division-by-0 prohibition.

### Findings

```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L36 => return (a & b) + (a ^ b) / 2;
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
```

### References
- [G008 - Use Shift Right/Left instead of Division/Multiplication if possible](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
- [EVM Opcodes](https://www.evm.codes/)

