## [G-01] abi.encode() is less efficient than abi.encodePacked()
abi.encodePacked will only use the minimal required memory to encode the data.

File EntryPoint.sol: [197](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L197)

File SmartAccount.sol: [136](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L136)

File SmartAccountNoAuth.sol: [136](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L136)