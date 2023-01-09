## [G-01] `x = x - y` costs less gas than `x -= y`, same for addition
`EntryPoint.sol:`
```
81: 	collected += _executeUserOp(i, ops[i], opInfos[i]);
101: 	totalOps += opsPerAggregator[i].userOps.length;
135: 	collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);
468: 	actualGas += preGas - gasleft();
```
`VerifyingSingletonPaymaster.sol`:
```
51: 	paymasterIdBalances[paymasterId] += msg.value;
58: 	paymasterIdBalances[msg.sender] -= amount;
128: 	paymasterIdBalances[extractedPaymasterId] -= actualGasCost;
```
You can replace all `-=` and `+=` occurrences to save gas

***

## [G-02] UNCHECKING ARITHMETICS OPERATIONS THAT CAN’T UNDERFLOW/OVERFLOW

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.
[Reference](https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic)

`EntryPoint.sol:`
```
100: 	for (uint256 i = 0; i < opasLen; i++) {
107: 	for (uint256 a = 0; a < opasLen; a++) {
112: 	for (uint256 i = 0; i < opslen; i++) {
128: 	for (uint256 a = 0; a < opasLen; a++) {
134: 	for (uint256 i = 0; i < opslen; i++) {
```

## [G-03] SPLITTING REQUIRE() STATEMENTS THAT USE `&&` SAVES GAS
`ModuleManager.sol`
```
34: 	require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
49: 	require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
68: 	require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```