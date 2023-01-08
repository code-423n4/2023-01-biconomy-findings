## Outdated Solidity version

Contracts use Solidity version 0.8.12 while the current version is 0.8.17. Old version of Solidity may contain bugs, security vulnerabilities (see references) and lack latest features. 

Affected:
All, e.g.:
[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L2)

Recommendation: 
While no direct security implications were identified in the tested codebase it is recommended to update to a newer Solidity version with no security vulnerabilities. Currently it is 0.8.17, as 0.8.16 had a vulnerability reported as well.

References: 
https://blog.soliditylang.org/2022/08/08/calldata-tuple-reencoding-head-overflow-bug/
https://blog.soliditylang.org/category/security-alerts/

## Redundant function

In `SmartAccount.sol` there are two functions performing the same operation - `function nonce(uint256 _batchId) public view virtual override returns (uint256)` [L97-99](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L97-L99) and `getNonce(uint256 batchId) public view returns (uint256)` [L155-159](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L155-L159).

Recommendation:
Consider removing on of these functions to reduce contract size