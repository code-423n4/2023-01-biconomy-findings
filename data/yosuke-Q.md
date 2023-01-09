## [YO Non-Critical-1] Outdated Compiler

### Handle
yosuke

## Vulnerability details
### Impact
The project is using Solidity compiler 0.6.11 which was released in February 2022, while the latest compiler version is 0.8.17. Using such an older version makes the project susceptible to any compiler bugs fixed since then and prevents it from leveraging the newly introduced features.

### Proof of Concept
`pragma solidity 0.8.12;`

### Recommended Mitigation Steps
Given Solidity’s fast release cycle, consider using a more recent version of the compiler, such as version 0.8.17.

## [YO Non-Critical-2] Unlocked Pragma

### Handle
yosuke

## Vulnerability details
### Impact
Some Solidity files specify in the header a version number of the format pragma solidity ^0.8.12. The caret (^) before the version number implies an unlocked pragma, meaning that the compiler will use the specified version or above.

### Proof of Concept
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L2
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L6
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L2
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L2
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L2
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L2
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L6
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L2
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L2


### Recommended Mitigation Steps
It’s usually a good idea to pin a specific version to know what compiler bug fixes and optimizations were enabled at the time of compiling the contract.

## [YO Non-Critical-3] Remaining TODOs

### Handle
yosuke

## Vulnerability details
### Impact
This is a minor suggestion.

A TODO is left in the code:EntryPoint.sol: // TODO: copy logic of gasPrice?

TODO usually mean something still have to be checked of done. This could lead to vulnerabilities if not verified.

### Proof of Concept

### Recommended Mitigation Steps
Check the TODO's and fix if necessary. Remove them afterwards