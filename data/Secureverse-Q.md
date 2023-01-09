### [Low-01] Multiple Solidity version used i.e some using ```^0.8.12``` and some using ```0.8.12```

Try to use Updated, stable, and most recent Solidity version to remain bug free

Below  Solidity ```^0.8.12```
```solidity 
File: aa-4337/interfaces/IAccount.sol
File: aa-4337/core/EntryPoint.sol
File: aa-4337/core/SenderCreator.sol
File: aa-4337/core/StakeManager.sol
File: aa-4337/utils/Exec.sol
File: aa-4337/interfaces/IAggregator.sol
File: aa-4337/interfaces/UserOperation.sol
File: aa-4337/interfaces/IStakeManager.sol
File: aa-4337/interfaces/IEntryPoint.sol
File: aa-4337/interfaces/IAggregatedAccount.sol
File: aa-4337/interfaces/IPaymaster.sol
File: paymasters/BasePaymaster.sol
```

Below Solidity ```0.8.12```
```solidity 
File: BaseSmartAccount.sol
File: Proxy.sol
File: SmartAccount.sol
File: SmartAccountFactory.sol
File: interfaces/ISignatureValidator.sol
File: interfaces/ERC1155TokenReceiver.sol
File: interfaces/ERC721TokenReceiver.sol
File: interfaces/ERC777TokensRecipient.sol
File: interfaces/IERC1271Wallet.sol
File: common/Singleton.sol
File: common/SignatureDecoder.sol
File: base/Executor.sol
File: common/SecuredTokenTransfer.sol
File: base/ModuleManager.sol
File: base/FallbackManager.sol
File: interfaces/IERC165.sol
File: libs/Math.sol
File: libs/LibAddress.sol
File: paymasters/PaymasterHelpers.sol
File: paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
File: common/Enum.sol
File: libs/MultiSend.sol
File: MultiSendCallOnly.sol
```



### [Low-2] Changing Owner should be a 2 Step-process
*Instances(1)*
```solidity
File: SmartAccount.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109-L114
```


### [Low-03] ETH could remain stuck in below contracts, as there are no functionality to resuce sent ETH to those contract 

*Instances(4)*
```solidity
File: BaseSmartAccount.sol

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L116-L120
```
```solidity
File: Proxy.sol

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16
```
```solidity
File: SmartAccount.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550
```
```solidity
File: lib/MultiSendCallOnly.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L21-L59
```

### [Low-04] Absence of Sanity check(zeroAddress check)
When updating fallbackHandler via ```setFallbackHandler()``` missing of zero-address check for function input
*Instances(1)*
```solidity
File: base/FallbackManager.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L26-L29
```



### [NC-1] Absence of error msg from ```require()``` statement
*Instances(1)*
```solidity
File: SmartAccount.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L528
```


### [NC-2] Events missing ```indexed``` key word that helpful in on-chain tracking
*Instances(3)*
```solidity
File: SmartAccount.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L64
```
```solidity
File: base/Executor.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L9
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L10
```



