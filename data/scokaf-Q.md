# Number 1: Floating Pragma

Vulnerability details

Impact

Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.8.12, ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

For reference, see https://swcregistry.io/docs/SWC-103
Proof of Concept

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L2

Tools Used

Manual Analysis

Recommended Mitigation Steps

Remove ^ in “pragma solidity ^0.8.12” and change it to “pragma solidity 0.8.12” to be consistent with the rest of the contracts.