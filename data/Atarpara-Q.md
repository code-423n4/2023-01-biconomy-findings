
### QA Report


#### Q-01 Use ReentrancyGuard instead of ReentrancyGuardUpgradeable

`ReentrancyGuardUpgradeable.sol` is recommend for the upgradble contract but `SmartAccount.sol` is the non-upgradable contract so use only `ReentrancyGuard.sol`.

**FileName:** *scw-contract/contracts/smart-wallet-contract/SmartAccount.sol*

```solidity=7
import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L7

#### Q-02 Remove Duplicate Comment

**FileName:** *scw-contract/contracts/smart-wallet-contract/base/Executor.sol*


``` solidity=8
    // Could add a flag fromEntryPoint for AA txn
    event ExecutionFailure(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
    event ExecutionSuccess(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);

    // Could add a flag fromEntryPoint for AA txn
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L8-L12


#### Q-03 Update Lastest Document Link 
Solidity current lastest docs link is https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_memory.html#layout-in-memory. 

**FileName:** *scw-contract/contracts/smart-wallet-contract/common/SecuredTokenTransfer.sol*

```solidity=20
            // We write the return value to scratch space.
            // See https://docs.soliditylang.org/en/v0.7.6/internals/layout_in_memory.html#layout-in-memory
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L20-L21


#### Q-04 Remove unimported Lib
Please remove unimported lib opeartion

**FileName:** *scw-contract/contracts/smart-wallet-contract/BaseSmartAccount.sol*

```solidity=34
    using UserOperationLib for UserOperation;
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L34



#### Q-05 Update comment as per EIP-1271 standard 

**FileName:** *scw-contract/contracts/smart-wallet-contract/interfaces/ISignatureValidator.sol*

```solidity=10
    /**
     * @dev Should return whether the signature provided is valid for the provided data
     * @param _data Arbitrary length data signed on the behalf of address(this)
     * @param _signature Signature byte array associated with _data
     *
     * MUST return the bytes4 magic value 0x20c13b0b when function passes.
     * MUST NOT modify state (using STATICCALL for solc < 0.5, view modifier for solc > 0.5)
     * MUST allow external calls
     */
```
https://github.com/code-423n4/2023-01-biconomy/blob/b94da74fc5e6ce27e63000d89a1835308dd8888d/scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol#L10-L18


#### Q-06 Remove Unnecessary Comment 

**FileName:** *scw-contract/contracts/smart-wallet-contract/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol*


```solidity=10
// import "../samples/Signatures.sol";
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L10


```solidity=25
    // possibly //  using Signatures for UserOperation;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L25

**FileName:** *scw-contract/contracts/smart-wallet-contract/paymasters/PaymasterHeplers.sol*
```solidity=15
    //@review
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L15


**FileName:** *scw-contract/contracts/smart-wallet-contract/SmartAccount.sol*
```solidity=33
    // Storage
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L33

```solidity=44
    // review? if rename wallet to account is must
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L44

```solidity=58
    // review
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L58

```solidity=68
    // nice to have
    // event SmartAccountInitialized(IEntryPoint indexed entryPoint, address indexed owner);
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L68-L69



```solidity=201
        //console.log("init %s", 21000 + msg.data.length * 8);
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L201

```solidity=235
            // uint256 extraGas;
            if (refundInfo.gasPrice > 0) {
                //console.log("sent %s", startGas - gasleft());
                // extraGas = gasleft();
                payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);
                emit WalletHandlePayment(txHash, payment);
            }
            // extraGas = extraGas - gasleft();
            //console.log("extra gas %s ", extraGas);
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L235-L243

```solidity=162
// init
    // Initialize / Setup
    // Used to setup
    // i. owner ii. entry point address iii. handler
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L162-L165

```solidity=255
        // uint256 startGas = gasleft();
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L255

```solidity=267
        // uint256 requiredGas = startGas - gasleft();
        //console.log("hp %s", requiredGas);
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L267-L268


```solidity=292
        //console.log("hpr %s", requiredGas);
        // Convert response to string and return via error message
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L292-L293

```solidity=487
    //@review
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L487


#### Q-07 Remove Unused Imported contract

**FileName:** *scw-contract/contracts/smart-wallet-contract/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol*


```solidity=7
import "@openzeppelin/contracts/proxy/utils/Initializable.sol";
import "@openzeppelin/contracts/utils/Address.sol";
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L7-L8


#### Q-08 Follow Same Comment format for all contracts

Follow same same multi-line comment format in all contrats.

**FileName:** *scw-contract/contracts/smart-wallet-contract/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol*

Correct
```solidity=45
    /**
     * add a deposit for this paymaster and given paymasterId (Dapp Depositor address), used for paying for transaction fees
     */
```
Slightly mismatched
```solidity=62
    /**
    this function will let owner change signer
    */
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L62-L64


**FileName:** *scw-contract/contracts/smart-wallet-contract/paymasters/BasePaymaster.sol*

```solidity=103
    /// validate the call is made from a valid entrypoint
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L103

**FileName:** *scw-contract/contracts/smart-wallet-contract/SmartAccount.sol*

Use same format for whole contact


#### Q-09 Code Doesn't format Properly

Format code with `forge fmt` or `prettier` for all contracts.
