## USE NAMED RETURNS  for functions WHERE APPROPRIATE
Files: 

- [SmartAccount.sol#L368](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L368)

Similar Code4rena contest report: https://code4rena.com/reports/2022-09-nouns-builder#g-06-use-named-returns-where-appropriate-3-instances

## x -= y costs more gas than x = x -y for state variables
Files: 

- [VerifyingSingletonPaymaster.sol#L58](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L58)

- [VerifyingSingletonPaymaster.sol#L128](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L128)
