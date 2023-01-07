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

* However, the `require()` function in solidity refunds all the remaining gas that we offered to pay ans reverts back all changes made in the contract.

Affected code:
* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16

* https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13



