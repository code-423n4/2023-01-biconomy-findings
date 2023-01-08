# Table of Contents

- [NC-01] Avoid the use of sensitive terms in favour of neutral ones
- [NC-02] Non-library/interface files should use fixed compiler versions, not floating ones
- [NC-03] Best practice is to prevent signature malleability
- [NC-04] Import exact functions 
- [L-01] Unused/Empty receive()/fallback() function
- [L-02] Require() should be used instead of assert()
- [L-03] Do more testing 

## [NC-01] Avoid the use of sensitive terms in favour of neutral ones

Use allowlist rather than whitelist. 

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L28

## [NC-02] Non-library/interface files should use fixed compiler versions, not floating ones

E.g. use 0.8.12 instead of ^0.8.12

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L2

## [NC-03] Best practice is to prevent signature malleability

Use OpenZeppelin’s ECDSA contract rather than calling ecrecover() directly

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L347

## [NC-04] Import exact functions 

Import exact functions instead of just entire contracts. 

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L4-L16

## [L-01] Unused/Empty receive() function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth)). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550

## [L-02] Require() should be used instead of assert()

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert()do. assert()should be avoided even past solidity version 0.8.0 as its documentation states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16

## [L-03] Do more testing 

Noted only 41% of the codebase is tested. It is recommended to do more testing. 




