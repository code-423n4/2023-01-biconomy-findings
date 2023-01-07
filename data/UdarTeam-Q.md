# [NC-01] Explicitly define import statements

## Description

The import syntax with curly braces helps locate where the building blocks (abstract contract, interface, library, etc.) come from and which files they are actually defined.

## Code location

- contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol: [L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L4)
- contracts/smart-contract-wallet/BaseSmartAccount.sol: [L8-L10](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L8-L10)
- contracts/smart-contract-wallet/SmartAccount.sol: [L4-L16](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L4-L16)
- contracts/smart-contract-wallet/base/ModuleManager.sol: [L4-L6](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L4-L6)
- contracts/smart-contract-wallet/base/FallbackManager.sol: [L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L4)
- contracts/smart-contract-wallet/SmartAccountFactory.sol: [L4-L5](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L4-L5)
- contracts/smart-contract-wallet/base/Executor.sol: [L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L4)
- contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol: [L4-L7](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L4-L7)
- contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol: [L12-L13](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L12-L13), [L15-L19](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L15-L19)
- contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol: [L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L4)
- contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol: [L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L4)
- contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol: [L4-L6](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L4-L6)
- contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol: [L12-L14](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L12-L14)
- contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol: [L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L4)
- contracts/smart-contract-wallet/paymasters/BasePaymaster.sol: [L7-L9](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L7-L9)
- contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol: [L4-L5](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L4-L5)
- contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol: [L5-L9](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L5-L9)

# [NC-02] Explicitly define ```uint``` size

## Description

Using ```uint256``` instead of ```uint``` makes code more consistent and also making the size of the data explicit reminds the developer and the reader how much data they've got to play with, which may help prevent or detect bugs.

## Code location 

- contracts/smart-contract-wallet/Proxy.sol: [L13](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L13)
- contracts/smart-contract-wallet/SmartAccount.sol: [L449](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449), [L460](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460), [L468](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L468), [L489](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489)
- contracts/smart-contract-wallet/SmartAccountFactory.sol: [L33](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L33), [L35](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L35), [L54](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L54), [L68](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L68), [L69](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L69), [L72](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L72)
- contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol: [L408](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L408)

