1. The entry point address must be a contract, currently, there is no input validation. You can provide an EOA as the entry point. 

Instances: 

- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L173
- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L173


Recommendation: 
Check that the entry point is a contract.

