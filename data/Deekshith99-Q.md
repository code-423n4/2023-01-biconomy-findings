# QA Report

## [NC-1] Unused imports can be removed
       here is the permalink of it 
VerifyingSingletonPaymaster.sol#L7
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L7

## [NC - 2] Open TODOs 
      It is recommend to complete the todos before deployment
      here is the permalink of it 
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255

## [NC-3] Use a more recent version of Solidity
      It is recommend to use to latest version i e 0.8.17
      instead of using 0.8.12 
## [NC-4] Avoid Floating Pragmas  The Version Should Be Locked
       Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple 
      different versions of applications, it may be a security risk for application implementations. 
      & It is recommended to pin to a concrete compiler version.