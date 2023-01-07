## QA Report
---
### OUTDATED COMPILER

`0.8.12` is missing the following features and bugfixes:  

[0.8.13](https://github.com/ethereum/solidity/releases/tag/v0.8.13)
[0.8.14](https://github.com/ethereum/solidity/releases/tag/v0.8.14)
[0.8.15](https://github.com/ethereum/solidity/releases/tag/v0.8.15)
[0.8.16](https://github.com/ethereum/solidity/releases/tag/v0.8.16)
[0.8.17](https://github.com/ethereum/solidity/releases/tag/v0.8.17)
***

### TODO &  REVIEW IN CODE

Ensure WIP code comments don't enter production.  

**TODO**  

[EntryPoint.sol:#L255](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255)

**REVIEW** 

[BaseSmartAccount.sol:#L59](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L59)
[BaseSmartAccount.sol:#L86](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L59)
[SmartAccount.sol:#L44](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L44)
[SmartAccount.sol:#L58](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L58)
[SmartAccount.sol#L149](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L149)
[SmartAccount.sol:#L313](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L313)
[SmartAccount.sol:#487](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L487)
[SmartAccountFactory.sol:#L14](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L14)
[PaymasterHelpers.sol:#L15](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L15)

***
### SPELLING MISTAKE
[SmartAccount.sol:#L228](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L228)

>`"substract" -> "subtract"`