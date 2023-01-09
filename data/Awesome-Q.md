# 1. Improve the Readability by following modularity principles

To make your Solidity code more modern and readable, it is recommended to update your import statements to only include the specific contracts or objects that you need.

This practice, known as modular programming, helps to keep the code cleaner and more organized.

To avoid unnecessarily polluting the source code with unused contracts or objects, it is recommended to use specific imports with curly braces. An example of this syntax is shown below:

```solidity
import {contract1, contract2} from "filename.sol";
```

By using specific imports in this way, you can ensure that your code follows the principles of modularity and is as clear and maintainable as possible.

Affected lines of code:

- [ModuleManager.sol#L4-L6](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L4-L6)

# 2. Use the `delete` operator to clear variables, instead of assigning a value of zero.

To clear variables, use the `delete` operator rather than assigning to zero. This conveys the intention more clearly and is more idiomatic.

For example on [line 52](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L52) you can refactor it as such:

```solidity
Line 52:    delete modules[module];
```

Affected line of code:

- [ModuleManager.sol#L52](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L52)

# 3. Open TODOs

Open TODOs can point to architecture or programming issues that still need to be resolved. Consider resolving them before deploying.

Affected lines of code:

- [EntryPoint.sol#L255](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255)

# 4. inadequate NatSpec

Contracts can use the Ethereum Natural Language Specification Format (NatSpec) to provide detailed documentation for functions, return variables, and other elements of the contract. This is done using a special type of comment within the contract code.

You can find more information about NatSpec and its usage in Solidity contracts at the following link:

- [NatSpec Format](https://docs.soliditylang.org/en/v0.8.16/natspec-format.html)

This is present in 1 file:

- [ERC777TokensRecipient.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol)
