
### [Low-01] Old Solidity
*Instances(36)*
The minimum required version must be [0.8.17](https://github.com/ethereum/solidity/releases/tag/v0.8.17); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/):

- ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against `calldatasize()` in all cases.
- Override Checker: Allow changing data location for parameters only when overriding external functions.

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

[0.8.17](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/)

- Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

Apart from these, there are several minor bug fixes and improvements.

### [Low-02] Multiple Solidity version used

Below contracts using Solidity ```0.8.12```
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
Below contracts using Solidity ```^0.8.12```
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


### [Low-03] Contracts has payable function to receive ETH, but there is no rescue function to send out ETH from those contract

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


### [Low-4] Changing Owner or setting any most important addresses should be a 2 Step-process
*Instances(1)*
```solidity
File: SmartAccount.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109-L114
```

### [Low-5] Could some lines of code(checks) remove from function ```init()```
As here is a ```initializer``` modifier that will help to initialize state variable only once through ```init()`` 
and ```owner``` and ```_entryPoint``` by default holds default value.

I mean this function only callable a single time, so no need to checks for ```owner == address(0)``` and ```address(_entryPoint) == address(0)```
so these 2 line could removed.

```solidity
    function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
        require(owner == address(0), "Already initialized");  // @audit no need to check
        require(address(_entryPoint) == address(0), "Already initialized");  // @audit no need to check
        require(_owner != address(0),"Invalid owner");
        require(_entryPointAddress != address(0), "Invalid Entrypoint");
        require(_handler != address(0), "Invalid Entrypoint");
        owner = _owner;
        _entryPoint =  IEntryPoint(payable(_entryPointAddress));
        if (_handler != address(0)) internalSetFallbackHandler(_handler);
        setupModules(address(0), bytes(""));
    }
```
*Instances(2)*
```solidity
File: SmartAccount.sol

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166-L176
```

### [Low-6] Absence of signature length check that ```signature``` that pass as a function parameter before ```v,r,s``` derived from it
*Instances(1)*
```solidity
File: common/SignatureDecoder.sol

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol#L10-L34
```

### [Low-07] While setting fallbackHandler via ```setFallbackHandler()``` missing of zero-address check for function input
*Instances(1)*
```solidity
File: base/FallbackManager.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L26-L29
```


### [Info-1] Events missing ```indexed``` key word that helpful in on-chain tracking
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


### [Info-2] Unused lines of comment, some comments that are perticularly for development not removed
*Instances(4)*
```solidity
File: SmartAccount.sol

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L44
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L53
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L58
```
```solidity
File: paymasters/BasePaymaster.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L50
```


### [Info-3] Absence of error msg from ```require()``` statement
*Instances(1)*
```solidity
File: SmartAccount.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L528
```