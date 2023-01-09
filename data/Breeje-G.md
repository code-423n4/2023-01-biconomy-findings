## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW | 7 |
| [GAS-2](#GAS-2) | X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES | 28 |
| [GAS-3](#GAS-3) | SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS | 3 |
| [GAS-4](#GAS-4) | NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS | 4 |

###  [GAS-1] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.

*Instances (7)*:
```solidity
File: aa-4337/core/EntryPoint.sol

100:      for (uint256 i = 0; i < opasLen; i++) {

107:      for (uint256 a = 0; a < opasLen; a++) {

112:      for (uint256 i = 0; i < opslen; i++) {

114:      opIndex++;

128:      for (uint256 a = 0; a < opasLen; a++) {

134:      for (uint256 i = 0; i < opslen; i++) {

136:      opIndex++;

```
[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)

###  [GAS-2] X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Instances (28)*:
```solidity
File: aa-4337/core/EntryPoint.sol

81:       collected += _executeUserOp(i, ops[i], opInfos[i]);

101:      totalOps += opsPerAggregator[i].userOps.length;

135:      collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);

468:      actualGas += preGas - gasleft();

```
[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)

```solidity
File: libs/Math.sol

148:       result += 1;

210:       result += 128;

214:       result += 64;

218:       result += 32;

222:       result += 16;

226:       result += 8;

230:       result += 4;

234:       result += 2;

237:       result += 1;

263:       result += 64;

267:       result += 32;

271:       result += 16;

275:       result += 8;

279:       result += 4;

283:       result += 2;

286:       result += 1;

314:       result += 16;

318:       result += 8;

322:       result += 4;

326:       result += 2;

329:       result += 1;

```
[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol)

```solidity
File: paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

51:      paymasterIdBalances[paymasterId] += msg.value;

58:      paymasterIdBalances[msg.sender] -= amount;

128:     paymasterIdBalances[extractedPaymasterId] -= actualGasCost;

```
[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol)

###  [GAS-3] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

Saves 3 gas per instance.

*Instances (3)*:
```solidity
File: base/ModuleManager.sol

34:      require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

49:      require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

68:      require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");

```
[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol)


###  [GAS-4] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

It is not necessary to have both a named return and a return statement.

*Instances (4)*:
```solidity
File: aa-4337/core/EntryPoint.sol

168:      function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost) {

```
[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L168)

```solidity
File: aa-4337/core/StakeManager.sol

18:     function getDepositInfo(address account) public view returns (DepositInfo memory info) { 

```
[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L18)

```solidity
File: paymasters/PaymasterHelpers.sol

27:     ) internal pure returns (bytes memory context) {

```
[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol)

```solidity
File: paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

98:    external view override returns (bytes memory context, uint256 deadline) {

```
[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L98)

