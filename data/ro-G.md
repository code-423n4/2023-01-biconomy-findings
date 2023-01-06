1. Unnecessary check: 
```solidity
require(owner == address(0), "Already initialized");
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L167
The "initializer" modifier checks if the wallet was already initialized. 

2. Unnecessary check: 
```solidity 
require(address(_entryPoint) == address(0), "Already initialized");
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L168
The "initializer" modifier checks if the wallet was already initialized. 

