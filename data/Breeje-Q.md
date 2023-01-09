
## QA Report


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | USE OF FLOATING PRAGMA | 13 |
| [L-2](#L-2) | MISSING CONTRACT-EXISTENCE CHECKS BEFORE LOW-LEVEL CALLS | 6 |
| [L-3](#L-3) | CONSIDER USING A TWO-STEP-TRANSFER OF OWNERSHIP | 2 |
| [L-4](#L-4) | POSSIBLE REENTRACY ATTACK | 2 |
| [NC-1](#NC-1) | USE REQUIRE INSTEAD OF ASSERT | 2 |
| [NC-2](#NC-2) | UNUSED FUNCTIONAL PARAMETER | 1 |
| [NC-3](#NC-3) | INSTEAD OF JUST REVERTING, FUNCTION CAN BE OMITTED | 1 |


###  [L-1] USE OF FLOATING PRAGMA

Impact: [swcregistry](https://swcregistry.io/docs/SWC-103)

*Instances (13)*:
* [BasePaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L2)
* [BaseAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol#L2)
* [BasePaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L2)
* [EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L6)
* [SenderCreator.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L2)
* [StakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L2)
* [IAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L2)
* [IAggregatedAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L2)
* [IEntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L6)
* [IPaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L2)
* [IStakeManager](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L2)
* [UserOperationLib.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L2)
* [Exec.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L2)

### [L-2] MISSING CONTRACT-EXISTENCE CHECKS BEFORE LOW-LEVEL CALLS

*Instances (6)*:
```solidity
File: Proxy.sol

28:        let result := delegatecall(gas(), target, 0, calldatasize(), 0, 0)
```
[Link to Code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L28)

```solidity
File: aa-4337/utils/Exec.sol

35:        success := delegatecall(txGas, to, add(data, 0x20), mload(data), 0, 0)

```
[Link to Code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L35)

```solidity
File: base/Executor.sol

23:        success := delegatecall(txGas, to, add(data, 0x20), mload(data), 0, 0)

28:        success := call(txGas, to, value, add(data, 0x20), mload(data), 0, 0)

```
[Link to Code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L23)

```solidity
File: libs/Multisend.sol

53:        success := call(gas(), to, value, data, dataLength, 0, 0)

56:        success := delegatecall(gas(), to, data, dataLength, 0, 0)

```
[Link to Code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Multisend.sol#L23)

### [L-3] CONSIDER USING A TWO-STEP-TRANSFER OF OWNERSHIP

*Instances (2)*:
```solidity
File: SmartAccount.sol

109:      function setOwner(address _newOwner) external mixedAuth {

```
[Link of Code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#109)

```solidity
File: paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

65:      function setSigner( address _newVerifyingSigner) external onlyOwner{
```
[Link of Code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#65)

### [L-4] POSSIBLE REENTRANCY ATTACK

In `deployCounterFactualWallet` and `deployWallet` method of `SmartAccountFactory.sol`, the State variable `isAccountExist` is changed after making all the External Calls. But It is recommended finishing all internal work (ie. state changes) first, and only then calling the external function.

###  [NC-1] USE REQUIRE INSTEAD OF ASSERT

Assert should not be used except for tests, require should be used

*Instances (2)*:
```solidity 

File:  Proxy.sol

16:        assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));

```
[Link to Code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16)

```solidity
File: common/Singleton.sol

13:       assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));

```
[Link to Code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13)

### [NC-2] UNUSED FUNCTIONAL PARAMETER

Functional Parameter not used can be removed.

```solidity
File: paymasters/PaymasterHelpers.sol

25:    UserOperation calldata op,

```
[Link to Code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L25)

### [NC-3] INSTEAD OF JUST REVERTING, FUNCTION CAN BE OMITTED 

There is a Function `deposit` in `VerifyingSingletonPaymaster.sol`, which just Reverts. So instead of just Reverting the Function can be omitted both from `VerifyingSingletonPaymaster.sol` and `BasePaymaster.sol` as it is of no use.

```solidity
File: paymasters/PaymasterHelpers.sol

41:    function deposit() public virtual override payable {
42:         revert("Deposit must be for a paymasterId. Use depositFor");
43:    }

```
[Link to Code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L25)
