# Gas Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[G-01]|Bit Shift(s) Not Used For MUL/DIV/MOD of Powers of 2|9|
|[G-02]|`uint256` Iterator Checked Each Iteration|7|
|[G-03]|&& In If Statement(s)|5|
|[G-04]|`i++`/`i+=1` Used Over `++i`|[+4](https://gist.github.com/Picodes/0a53b4abfc71e0b9998e8b09aa283fb3)|
|[G-05]|`unchecked` Not Used on Overflow / Underflow Proof Arithmetic|4|
|[G-06]|&& In Require Statement(s)|3|

### [G-01] Bit Shift(s) Not Used For MUL/DIV/MOD of Powers of 2

Bitwise operations usually save on gas. Expressions that deal with unsigned integers, a power of 2 and the operations MUL/DIV/MOD can often be re-written with bitwise shifts.

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol*
Links: [200](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L200), [224](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L224).
```solidity
200:	uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
224:	require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
```

*/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol*
Links: [36](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L36).
```solidity
36:	return (a & b) + (a ^ b) / 2;
```

### [G-02] `uint256` Iterator Checked Each Iteration

A `uint256` iterator will not overflow before the check variable overflows. Unchecking the iterator increment saves gas.
**Example**
```solidity
//From
for (uint256 i; i < len; ++i) {
	//Do Something
}
//To
for (uint256 i; i < len;) {
	//Do Something
	unchecked { ++i; }
}
```

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol*
Links: [74](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L74), [80](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L80), [100](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100), [107](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L107), [112](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L112), [128](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L128), [134](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L134).
```solidity
74:	for (uint256 i = 0; i < opslen; i++) {
80:	for (uint256 i = 0; i < opslen; i++) {
100:	for (uint256 i = 0; i < opasLen; i++) {
107:	for (uint256 a = 0; a < opasLen; a++) {
112:	for (uint256 i = 0; i < opslen; i++) {
128:	for (uint256 a = 0; a < opasLen; a++) {
134:	for (uint256 i = 0; i < opslen; i++) {
```

### [G-03] && In If Statement(s)

Seperating if statements saves gas.
**Example**
```solidity
//From
if (a != HIGH && b != LOW) {
	//Do Something
}
//To
if (a != HIGH) {
	if (b != LOW) {
		//Do Something
	}
}
```

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol*
Links: [147](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L147).
```solidity
147:	if (rounding == Rounding.Up && mulmod(x, y, denominator) > 0) {
```

*/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol*
Links: [303](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L303), [321](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L321), [365](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L365), [410](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L410).
```solidity
303:	if (mUserOp.paymaster != address(0) && mUserOp.paymaster.code.length == 0) {
321:	if (_deadline != 0 && _deadline < block.timestamp) {
365:	if (_deadline != 0 && _deadline < block.timestamp) {
410:	if (paymasterDeadline != 0 && paymasterDeadline < deadline) {
```

### [G-04] `i++`/`i+=1` Used Over `++i`

`++i` increments `i` directly. When using `i++`/`i+=1` solc must create a temporary value, thus resulting in higher gas.

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol*
Links: [148](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L148), [237](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L237), [286](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L286), [329](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L329).
```solidity
148:	result += 1;
237:	result += 1;
286:	result += 1;
329:	result += 1;
```

### [G-05] `unchecked` Not Used on Overflow / Underflow Proof Arithmetic

Modern Solidity will automatically revert if a value overflows/underflows. More computation is used in order to force an overflow/underflow revert by default. When overflows/underflows are not possible (likely due to an earlier check), code can be placed inside an `unchecked` brace to save gas.

```solidity
unchecked {
	//Code
}
```

The findings below display the first line which allows the second line to go `unchecked`. ONLY the second line is suggested to be put in an `unchecked` brace.

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol*
Links: [344 / 347](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L344-L347).
```solidity
344:	else if(v > 30) {
347:	_signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);
```

*/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol*
Links: [353 / 354](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L353-L354), [359 / 362](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L359-L362).
```solidity
353:	require(verificationGasLimit > gasUsedByValidateAccountPrepayment, "AA41 too little verificationGas");
354:	uint256 gas = verificationGasLimit - gasUsedByValidateAccountPrepayment;
```
```solidity
359:	if (deposit < requiredPreFund) {
362:	paymasterInfo.deposit = uint112(deposit - requiredPreFund);
```

*/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol*
Links: [117 / 118](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L117-L118).
```solidity
117:	require(withdrawAmount <= info.deposit, "Withdraw amount too large");
118:	info.deposit = uint112(info.deposit - withdrawAmount);
```

### [G-06] && In Require Statement(s)

Seperating require statements saves gas.
**Example**
```solidity
//From
require(a != HIGH && b != LOW);
//To
require(a != HIGH);
require(b != LOW);
```

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol*
Links: [34](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34), [49](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49), [68](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68).
```solidity
34:	require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
49:	require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
68:	require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```