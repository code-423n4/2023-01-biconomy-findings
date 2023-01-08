Use ++i instead of i++ to save gas
===============

this gas optimization can be done in the following lines:

`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L74`
`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L80`
`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L99`
`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L112`
`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L128`
`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L134`

Use delegatecall or callcode instead of call to save gas
===================================
`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L28`

Use `REQUIRE` Instead of `ASSERT`
======================
The solidity functions `assert()` and `require()` are used for error handling in solidity. Since solidity makes uses of state reverting error handling this means all changes made to the contract or any of its subcalls are undone if an error is occured.

Since both of this functions are quite similar and they do almost the same thing here are the differences between them:

* The `assert()` function when false uses up all the remaining gas and reverts all changes made.

* However, the `require()` function in solidity refunds all the remaining gas that we offered to pay and reverts back all changes made in the contract.

Affected code:
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16

* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13

Avoid using compound assignment operator in state variables
========================================
Using compound assignment operators such as `state +=x` or `state -=x` is more expensive then using operator assignment `state= state + x` or `state = state - x`.

* Proof Of Concept(without optimization)
```
pragma solidity ^0.8.12;

//Not optimized contract
contract TestA{
    uint private num;
    function testA() public{
        num += 1;
    }
}

//Optimized contract
contract TestB{
    uint private num;
    function testB() public{
        num = num + 1;
    }
}
```
Gas saving executing:15 per entry
```
contract TestA = 50027gas
contract TestB =  50012gas
```
Affaected Code:
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L148
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L210-L237
*https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L263-L286
*https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L81
*https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L101
*https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L135
*https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L468

Total gas saved = 15x7 = 35


Use `calldata` instead of  `memory`
======================
Some functions/methods that are declared as `external` and the arguments are defined as `memory` cost more gas instead of `calldata`.
To save a significant amount it is better to use `calldata` in the arguments of an `external` function:

Recomended changes:
```
Remove: function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost)
Change To : function innerHandleOp(bytes calldata callData, UserOpInfo calldata opInfo, bytes calldata context) external returns (uint256 actualGasCost)

Remove: function getSenderAddress(bytes memory initCode) external;
Change To:function getSenderAddress(bytes calldata initCode) external;
```
Affected code:
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L168
*https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L156


No need to set default value for variables
===========================
If a variable is not set/initialized the deafault value is assumed to be (0,0x0...,false) depending on the variable data types. Gas is wasted by simply initializing a variable with its default value.

Proof of Concept:
```
pragma solidity ^0.8.12;

//Not optimized contract
contract TestA{
    function testA() public view returns(uint){
        uint a = 0;
        return a;
    }
    
}
//optimized contract
contract TestB{
    function testB() public view returns(uint){
        uint a;
        return a;
    }
    
}
```
Gas saving execution: 8 per entry.
```
contract TestA: 21392 gas
contract TestB: 21384 gas
```
Affected code:
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L78
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L74
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L80
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L99
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L106
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L107
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L112
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L126
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L128
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L134
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L313
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L119
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L206
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L259
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L310

Estimated gas savings = 8x16 = 128 gas