### [L-01] Low level calls with solidity version 0.8.12 can result in optimizer bug.

The project contracts in scope are using low level calls with solidity version before 0.8.14 which can result in optimizer bug.
https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

Simliar findings in Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#5-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug

Recommended Mitigation Step
Consider upgrading to at least v0.8.15 or solidity stable version v0.8.17.




### [L-02] Attend to the todos on the project because TODOS indicate unfinished work which may affect the overall project.
The code that contains “todos” reflects that the code can change a posteriori, prior release, with or without audit.

file:

[[EntryPoint.sol#L255](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.soll)]

