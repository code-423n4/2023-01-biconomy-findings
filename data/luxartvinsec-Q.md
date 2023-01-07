
# 1st report for : BaseSmartAccount.sol https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

## 1. Validation of the `validateUserOp` method is susceptible to replay attacks

Summary: The `validateUserOp` method in the `BaseSmartAccount` contract is susceptible to replay attacks. The method does not check for a replay protection mechanism, such as a nonce or a timestamp, and only updates the nonce if the `initCode` field of the `UserOperation` struct is empty. This allows an attacker to potentially reuse a previously signed `UserOperation` struct in a new transaction, potentially causing unintended consequences.

Impact: An attacker could potentially reuse a previously signed `UserOperation` struct in a new transaction, potentially causing unintended consequences. This could lead to loss of funds or other unintended consequences.

Recommendation: It is recommended to implement a replay protection mechanism, such as a nonce or a timestamp, in the `validateUserOp` method to prevent replay attacks. This can be done by checking the current nonce or timestamp in the `_validateAndUpdateNonce` function and only updating the nonce or timestamp if it is valid.

## 2. Improper Validation of Gas Limit in `BaseSmartAccount.validateUserOp`

Summary: The `BaseSmartAccount.validateUserOp` function does not properly validate the gas limit of `userOp` before executing it. This allows an attacker to cause an accidental denial of service (DoS) attack by specifying a very large gas limit in the `userOp` struct.

Impact: An attacker could send a `userOp` with an unreasonably large gas limit to the `BaseSmartAccount` contract, potentially causing other operations within the same block to fail due to gas exhaustion. This could lead to a temporary disruption of services and loss of availability for the affected contract.

Recommendation: The gas limit of `userOp` should be properly validated before executing the operation. This could be done by comparing the gas limit to some reasonable maximum value or by checking that the gas limit is not significantly larger than the average gas usage of previous operations.

## 3. Incorrect handling of `initCode` field in `validateUserOp` function

Summary: The `validateUserOp` function does not correctly handle the `initCode` field in the `UserOperation` struct. If the `initCode` field is not empty, the function does not call `_validateAndUpdateNonce`, which can allow attackers to bypass nonce validation.

Impact: Attackers could potentially replay transactions or create new accounts without incrementing the nonce, leading to a loss of funds and potentially leading to contract vulnerabilities being exploited.

Recommendation: The `validateUserOp` function should include a check for the `initCode` field before calling `_validateAndUpdateNonce`. If `initCode` is not empty, `_validateAndUpdateNonce` should still be called to ensure proper nonce handling.

# 2nd report for : Proxy.sol https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol

## 1. Revert message not returned correctly in fallback function

Summary: 
The `fallback` function in the contract uses a `switch` statement to determine whether to revert the transaction or not based on the result of the `delegatecall` to the target contract. However, the `switch` statement is checking for `result == 0` to determine if the transaction should be reverted, but the Solidity documentation states that `delegatecall` returns `false` on failure and `true` on success. This means that the transaction will not be properly reverted when an error occurs in the target contract, leading to potentially unintended behavior.

Impact: 
If an error occurs in the target contract, the transaction will not be properly reverted, potentially leading to unintended behavior.

Recommendation: 
Modify the `switch` statement in the `fallback` function to check for `result == false` instead of `result == 0` to correctly handle the case where an error occurs in the target contract.

# 3rd report for : Singleton.sol https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol

## 1. Stored Implementation Slot Tampering

Summary: 
The contract has a constant defined as the keccak-256 hash of `biconomy.scw.proxy.implementation` subtracted by 1, which is used as a storage slot for the implementation address. However, this storage slot can be tampered with by any contract that has write access to storage.

Impact: 
This vulnerability allows an attacker to change the implementation of the contract. This could potentially lead to the attacker being able to execute arbitrary code within the contract, or disrupt the intended functionality of the contract.

Recommendation:
The storage slot for the implementation address should be protected from tampering. One way to do this is to use the `private` visibility specifier to prevent external contracts from accessing it.
Alternatively, the implementation address can be stored in the contract's storage using the `address` data type, which is a secure storage slot that can only be modified by the contract itself.
The constant that is used to define the storage slot should not be relied upon as a security measure, as it can be easily tampered with. Instead, a more secure mechanism should be used to protect the implementation address.

# 4th report for : ModuleManager.sol https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

## 1. Unauthorized module execution vulnerability

Summary: 
There is a vulnerability in the `execTransactionFromModule` function in the `ModuleManager` contract that allows any address to call the function and execute transactions on behalf of the contract. The issue is caused by the lack of a check to verify that the caller of the function is a whitelisted module.

Impact: 
An attacker can exploit this vulnerability to execute arbitrary transactions on behalf of the contract, potentially leading to financial loss or reputation damage for the contract owner.

Recommendation: 
To fix this issue, add a check at the beginning of the `execTransactionFromModule` function to verify that the caller is a whitelisted module by checking that `msg.sender` is not equal to `SENTINEL_MODULES` and that `modules[msg.sender]` is not equal to `address(0)`.

# 5th report for : SecuredTokenTransfer.sol https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol

## 1. Improper input validation in SecuredTokenTransfer contract

Summary: 
The `SecuredTokenTransfer` contract does not properly validate the input parameters passed to the `transferToken` function, specifically the `token` and `receiver` addresses. This allows an attacker to potentially transfer tokens to an unintended address or perform other malicious actions.

Impact: 
An attacker could exploit this vulnerability to transfer tokens to an unintended address, potentially leading to financial loss for the intended recipient.

Recommendation: 
It is recommended to add input validation checks for the `token` and `receiver` parameters to ensure that they are valid and intended addresses before executing the transfer. This can be done using the `require` statement to ensure that the addresses are not null or the zero address. It is also recommended to consider adding additional checks as necessary to ensure the integrity and security of the transfer process.

# 6th report for : SmartAccountFactory.sol https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

## 1. Old Solidity version

Summary:
The solidity version specified in the contract's pragma statement is incorrect. The specified version is 0.8.12, but the current version is 0.8.17.

Impact:
Using an incorrect solidity version can lead to unintended behavior and potentially vulnerabilities in the contract. It is recommended to always use the latest version of solidity to take advantage of any bug fixes and security improvements.

Recommendation:
Update the solidity version in the pragma statement to the latest version (currently 0.8.17). It is also a good practice to specify the exact version of solidity used in the project's documentation and dependency management tools, such as Truffle or npm.

## 2. create2 function called with a zero address

Summary:
In the `deployCounterFactualWallet` function, the `create2` function is called with an address value of 0x0. This could potentially cause the contract to create an incorrectly initialized smart contract.

Impact:
If the `create2` function is called with a zero address, it could result in the creation of an incorrectly initialized smart contract. This could lead to unexpected behavior and potentially even security vulnerabilities in the contract.

Recommendation:
It is recommended to ensure that the `create2` function is not called with a zero address. One way to do this would be to use a require statement to check that the `_owner` parameter is not equal to the zero address before calling the `create2` function.
