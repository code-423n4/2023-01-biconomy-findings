### [Gas-01] ```onlyOwner``` functions Should make as ```payable```, Those function which reverts when called by normal user should make as payable
*Instances(10)*
```solidity
File: SmartAccount.sol

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449-L453
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455-L458
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460-L463
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465-L473
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L536-L538
```
```solidity
File: paymasters/BasePaymaster.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L67
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L75
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99
```
```solidity
File: paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65
```

### [Gas-02] ``uncheck``` Arithmatic Operation that would never be ````Underflow/Overflow```
*Instances(1)*
```solidity
File: base/ModuleManager.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L124
```


### [Gas-03] Should Use ```require()``` Instead of ```assert()```
assert() ==> revert all state variable change and consume all remaining gas
require() ==>  reverts all state variable change and return all remaining gas to caller

From gas saving perspective its recommended to use require() instead of assert()

*Instances(2)*
```solidity
File: Proxy.sol

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16
```
```solidity
File: common/Singleton.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13
```


### [Gas-04] Instead of using && Operator in ```require()``` by splitting them can save gas
*Instances(3)*
```solidity
File: base/ModuleManager.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68
```



### [Gas-05] Divided by 2 always Bit Shifted 
*Instances(1)*
```solidity
File: libs/Math.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L36
``` 


### [Gas-06] ```abi.encodePacked()``` should used here rather than ```abi.encode()```
```solidity
return abi.encode(data.paymasterId);
```
Should replace with

```solidity
return abi.encodePacked(data.paymasterId);
```
*Instances(1)*
```solidity
File: paymasters/PaymasterHelpers.sol
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L28
```
