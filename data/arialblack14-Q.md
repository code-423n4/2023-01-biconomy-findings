# QA REPORT

## [L-1] Use of `block.timestamp`

### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the *[Entropy Illusion](https://hacken.io/discover/most-common-smart-contract-vulnerabilities/#Entropy_Illusion)* for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### ✅ Recommendation
Use oracle instead of `block.timestamp`

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L321|[if (_deadline != 0 && _deadline < block.timestamp) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L321 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L365|[if (_deadline != 0 && _deadline < block.timestamp) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L365 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L84|[uint64 withdrawTime = uint64(block.timestamp) + info.unstakeDelaySec;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L84 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L101|[require(info.withdrawTime <= block.timestamp, "Stake withdrawal is not due");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L101 )|
---

## [L-2] Events not emitted for important state changes.

### Description
Emitting events allows monitoring activities with off-chain monitoring tools.

### ✅ Recommendation
Emit event when an important state change occurs.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455|[function pullTokens(address token, address dest, uint256 amount) external onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460|[function execute(address dest, uint value, bytes calldata func) external onlyOwner{](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465|[function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90|[function unlockStake() external onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99|[function withdrawStake(address payable withdrawAddress) external onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65|[function setSigner( address _newVerifyingSigner) external onlyOwner{](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65 )|
---

## [L-3] Unused/empty `receive()`/`fallback()` function.

### Description
If the intention is for the Ether to be used, the function should call another function, otherwise, it should revert 

### ✅ Recommendation


### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550|[receive() external payable {}](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550 )|
---

## [L-4] Avoid hardcoded values.

### Description
It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code.

### ✅ Recommendation
Use the selector instead of the raw value.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L11|[bytes32 internal constant _IMPLEMENTATION_SLOT = 0x37722d148fb373b961a84120b6c8d209709b45377878a466db32bbc40d95af26;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L11 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L42|[bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH = 0x47e79534a245952e8b16893a336b85a3d9ea9fa8c573f3d803afb92a79469218;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L42 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L48|[bytes32 internal constant ACCOUNT_TX_TYPEHASH = 0xc2595443c361a1f264c73470b9410fd67ac953ebd1a3ae63a2f514f3f014cf07;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L48 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L12|[bytes32 internal constant FALLBACK_HANDLER_STORAGE_SLOT = 0x6c9a6c4a39284e37ed1cf53d337577d14212a4870fb976a4366c693b939918d5;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L12 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L10|[bytes32 internal constant _IMPLEMENTATION_SLOT = 0x37722d148fb373b961a84120b6c8d209709b45377878a466db32bbc40d95af26;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L10 )|
---

## [L-5] Open TODOs

### Description
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

### ✅ Recommendation
Implement TODO and remove.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255|[// TODO: copy logic of gasPrice?](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255 )|
---

## [N-1] Missing timelock for critical changes.

### Description
A timelock should be added to functions that perform critical changes.

### ✅ Recommendation
TODO: Add Timelock for the following functions.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455|[function pullTokens(address token, address dest, uint256 amount) external onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460|[function execute(address dest, uint value, bytes calldata func) external onlyOwner{](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465|[function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90|[function unlockStake() external onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99|[function withdrawStake(address payable withdrawAddress) external onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65|[function setSigner( address _newVerifyingSigner) external onlyOwner{](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65 )|
---

## [N-2] Use scientific notation (e.g.`1e18`) rather than large multiples of 10 (e.g. 1000000)

### Description
Use scientific notation (e.g.`1e18`) rather than large multiples of 10, that could lead to errors.

### ✅ Recommendation
Use scientific notation instead of large multiples of 10.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L22|[let success := call(sub(gas(), 10000), token, 0, add(data, 0x20), mload(data), 0, 0x20)](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L22 )|
---

## [N-3] Use scientific notation (e.g.`1e18`) rather than exponentiation (e.g.`10**18`).

### Description
Use scientific notation (e.g.`1e18`) rather than exponentiation (e.g.`10**18`) that could lead to errors.

### ✅ Recommendation
Use scientific notation instead of exponentiation.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L261|[if (value >= 10**64) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L261 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L262|[value /= 10**64;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L262 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L265|[if (value >= 10**32) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L265 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L266|[value /= 10**32;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L266 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L269|[if (value >= 10**16) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L269 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L270|[value /= 10**16;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L270 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L273|[if (value >= 10**8) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L273 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L274|[value /= 10**8;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L274 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L277|[if (value >= 10**4) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L277 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L278|[value /= 10**4;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L278 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L281|[if (value >= 10**2) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L281 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L282|[value /= 10**2;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L282 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L285|[if (value >= 10**1) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L285 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L299|[return result + (rounding == Rounding.Up && 10**result < value ? 1 : 0);](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L299 )|
---



## [N-4] Lines are too long.

### Description
Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

### ✅ Recommendation
Reduce number of characters per line to improve readability.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L46|[//     "AccountTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L46 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L239|[payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L239 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L357|[///      Since the `estimateGas` function includes refunds, call this method to get an estimated of the costs that are deducted from the safe with `execTransaction`](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L357 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489|[function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L319|[try IAccount(sender).validateUserOp{gas : mUserOp.verificationGasLimit}(op, opInfo.userOpHash, aggregator, missingAccountFunds) returns (uint256 _deadline) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L319 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L349|[function _validatePaymasterPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, uint256 requiredPreFund, uint256 gasUsedByValidateAccountPrepayment) internal returns (bytes memory context, uint256 deadline) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L349 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L440|[function _handlePostOp(uint256 opIndex, IPaymaster.PostOpMode mode, UserOpInfo memory opInfo, bytes memory context, uint256 actualGas) private returns (uint256 actualGasCost) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L440 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L28|[event UserOperationEvent(bytes32 indexed userOpHash, address indexed sender, address indexed paymaster, uint256 nonce, bool success, uint256 actualGasCost, uint256 actualGasUsed);](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L28 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L16|[* @param paymasterAndData if set, this field hold the paymaster address and "paymaster-specific-data". the paymaster will pay for the transaction instead of the sender](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L16 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L10|[@dev An ERC1155-compliant smart contract MUST call this function on the token recipient contract, at the end of a `safeTransferFrom` after the balance has been updated.](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L10 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L31|[@dev An ERC1155-compliant smart contract MUST call this function on the token recipient contract, at the end of a `safeBatchTransferFrom` after the balances have been updated.](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L31 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L32|[This function MUST return `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))` (i.e. 0xbc197c81) if it accepts the transfer(s).](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L32 )|
---

## [N-5] For modern and more readable code, update import usages.

### Description
Solidity code is cleaner in the following way: On the principle that clearer code is better code, you should import the things you want to use. Specific imports with curly braces allow us to apply this rule better. Check out this [article](https://betterprogramming.pub/solidity-tutorial-all-about-imports-c65110e41f3a)

### ✅ Recommendation
Import like this: `import {contract1 , contract2} from "filename.sol";`

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L8|[import "@account-abstraction/contracts/interfaces/IAccount.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L8 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L9|[import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L9 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L10|[import "./common/Enum.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L10 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L4|[import "./libs/LibAddress.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L5|[import "@openzeppelin/contracts/token/ERC20/IERC20.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L5 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L6|[import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L6 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L7|[import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L7 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L8|[import "./BaseSmartAccount.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L8 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L9|[import "./common/Singleton.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L9 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L10|[import "./base/ModuleManager.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L10 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L11|[import "./base/FallbackManager.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L11 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L12|[import "./common/SignatureDecoder.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L12 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L13|[import "./common/SecuredTokenTransfer.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L13 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L14|[import "./interfaces/ISignatureValidator.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L14 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L15|[import "./interfaces/IERC165.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L15 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L16|[import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L16 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L4|[import "./Proxy.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L5|[import "./BaseSmartAccount.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L5 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L12|[import "../interfaces/IAccount.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L12 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L13|[import "../interfaces/IPaymaster.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L13 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L15|[import "../interfaces/IAggregatedAccount.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L15 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L16|[import "../interfaces/IEntryPoint.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L16 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L17|[import "../utils/Exec.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L17 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L18|[import "./StakeManager.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L18 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L19|[import "./SenderCreator.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L19 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L4|[import "../interfaces/IStakeManager.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L4|[import "./UserOperation.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L4|[import "./UserOperation.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L5|[import "./IAccount.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L5 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L6|[import "./IAggregator.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L6 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L4|[import "./UserOperation.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L12|[import "./UserOperation.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L12 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L13|[import "./IStakeManager.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L13 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L14|[import "./IAggregator.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L14 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L4|[import "./UserOperation.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L4|[import "../common/Enum.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L4|[import "../common/SelfAuthorized.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L4|[import "../common/Enum.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L5|[import "../common/SelfAuthorized.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L5 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L6|[import "./Executor.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L6 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L4|[import "../interfaces/ERC1155TokenReceiver.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L5|[import "../interfaces/ERC721TokenReceiver.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L5 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L6|[import "../interfaces/ERC777TokensRecipient.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L6 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L7|[import "../interfaces/IERC165.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L7 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L7|[import "@openzeppelin/contracts/access/Ownable.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L7 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L8|[import "@account-abstraction/contracts/interfaces/IPaymaster.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L8 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L9|[import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L9 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L4|[import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L4 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L5|[import "@account-abstraction/contracts/interfaces/UserOperation.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L5 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L5|[import "../../BasePaymaster.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L5 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L6|[import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L6 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L7|[import "@openzeppelin/contracts/proxy/utils/Initializable.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L7 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L8|[import "@openzeppelin/contracts/utils/Address.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L8 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L9|[import "../../PaymasterHelpers.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L9 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L10|[// import "../samples/Signatures.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L10 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/utils/GasEstimatorSmartAccount.sol#L4|[import "../SmartAccountFactory.sol";](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/utils/GasEstimatorSmartAccount.sol#L4 )|
---
