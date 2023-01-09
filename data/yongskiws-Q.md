### [L-1] Dont use tx.origin and tx.gasprice
Consider tx.origin and tx.gasprice is a global variable in Solidity that returns the address of the account that sent the transaction. Using the variable could make a contract vulnerable if an authorized account calls a malicious contract impersonate a user using a third party contract.

``` solidity
contracts/SmartAccount.sol
257:         address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
258:         if (gasToken == address(0)) {
259:             // For ETH we will only adjust the gas price to not be higher than the actual used gas price
260:             payment = (gasUsed + baseGas) * (gasPrice < tx.gasprice ? gasPrice : tx.gasprice);
261:             (bool success,) = receiver.call{value: payment}("");
262:             require(success, "BSA011");
263:         } else {
264:             payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);
265:             require(transferToken(gasToken, receiver, payment), "BSA012");
266:         }


281:         address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
282:         if (gasToken == address(0)) {
283:             // For ETH we will only adjust the gas price to not be higher than the actual used gas price
284:             payment = (gasUsed + baseGas) * (gasPrice < tx.gasprice ? gasPrice : tx.gasprice);
285:             (bool success,) = receiver.call{value: payment}("");
286:             require(success, "BSA011");
287:         } else {
288:             payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);
289:             require(transferToken(gasToken, receiver, payment), "BSA012");
290:         }

511:         require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
512:         return 0;

```

### [L-2] Consider an ether lockout may occur in the function
If the summoner sends ether to this contract, then the ether will be locked in the contract and cannot be retrieved, which can lead to an ether lock
Remove the payable attribute or add a withdraw function.
``` solidity
contracts\libs\MultiSend.sol
26:  function multiSend(bytes memory transactions) public payable {
contracts\libs\MultiSendCallOnly.sol
21:     function multiSend(bytes memory transactions) public payable {
```

### [L-3] Consider the unused return value
While not consuming more gas with the Optimizer enabled: using both named returns and a return statement isn't necessary. Removing one of those can improve code clarity:
Using both named returns and a return statement isn't necessary.
Removing unused named return variables can reduce gas usage and improve code clarity. To save gas and improve code quality: consider using only one of those.

``` solidity 
contracts\aa-4337\core\EntryPoint.sol
48: function _executeUserOp(uint256 opIndex, UserOperation calldata userOp, UserOpInfo memory opInfo) private returns (uint256 collected) {
52:         try this.innerHandleOp(userOp.callData, opInfo, context) returns (uint256 _actualGasCost) {
459:                  catch Error(string memory reason) {
289:     function _validateAccountPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, address aggregator, uint256 requiredPrefund)
290:     internal returns (uint256 gasUsedByValidateAccountPrepayment, address actualAggregator, uint256 deadline) {
289:     function _validateAccountPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, address aggregator, uint256 requiredPrefund)
290:     internal returns (uint256 gasUsedByValidateAccountPrepayment, address actualAggregator, uint256 deadline) {
319:  try IAccount(sender).validateUserOp{gas : mUserOp.verificationGasLimit}(op, opInfo.userOpHash, aggregator, missingAccountFunds) returns (uint256 _deadline) {
325:    } catch Error(string memory revertReason) {
307:      try IAggregatedAccount(sender).getAggregator() returns (address userOpAggregator) {
349: function _validatePaymasterPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, uint256 requiredPreFund, uint256 gasUsedByValidateAccountPrepayment) internal returns (bytes memory context, uint256 deadline) {
363:         try IPaymaster(paymaster).validatePaymasterUserOp{gas : gas}(op, opInfo.userOpHash, requiredPreFund) returns (bytes memory _context, uint256 _deadline){

```



### [L-4] Open TODO
contracts\aa-4337\core\EntryPoint.sol

255:         // TODO: copy logic of gasPrice?

### [L-5] Mixing and Outdated compiler

The pragma version used are:
``` solidity
pragma solidity ^0.8.12;
pragma solidity ^0.8.4;
pragma solidity ^0.8.9;
```
Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.

The minimum required version must be 0.8.17; otherwise, contracts will be affected by the following important bug fixes:

0.8.14:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.
0.8.15

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.
0.8.16

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.
0.8.17

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.
Apart from these, there are several minor bug fixes and improvements.


### [L-6] consider validating Functions that send to arbitrary destinations
EG then if calling the call method fails (success = false), then the function will raise an exception with the message "Failed". This exception can be handled with a block catch in the function that calls _payPrefund to handle delivery failures

``` solidity
contracts\BaseSmartAccount.sol
106:     function _payPrefund(uint256 missingAccountFunds) internal virtual {
107:         if (missingAccountFunds != 0) {
108:             (bool success,) = payable(msg.sender).call{value : missingAccountFunds, gas : type(uint256).max}("");
109:           --    (success);
 ++ example require(success, "Failed");
110:             //ignore failure (its EntryPoint's job to verify, not account.)
111:         }
112:     }

```

### [NC-1]  consider update dependencies
@account-abstraction/contracts ^0.3.0
@openzeppelin/contracts ^4.7.3 
@openzeppelin/contracts-upgradeable  ^4.7.3

### [NC-2] warning compile error
Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
  --> contracts/paymasters/PaymasterHelpers.sol:25:9:
   |
25 |         UserOperation calldata op,
   |         ^^^^^^^^^^^^^^^^^^^^^^^^^
