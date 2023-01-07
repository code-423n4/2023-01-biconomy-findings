## Compiler version Pragma is non-specific

### Description

For non-library contracts, floating pragmas may be a security risk for application implementations, since a known vulnerable compiler version may accidentally be selected or security tools might fallback to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

### Findings

```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L2 => pragma solidity ^0.8.12;
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L6 => pragma solidity ^0.8.12;
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L2 => pragma solidity ^0.8.12;
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L2 => pragma solidity ^0.8.12;
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L6 => pragma solidity ^0.8.12;
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L2 => pragma solidity ^0.8.12;
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L2 => pragma solidity ^0.8.12;
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L2 => pragma solidity ^0.8.12;
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L2 => pragma solidity ^0.8.12;
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L2 => pragma solidity ^0.8.12;
```

### Resources

- [L003 - Unspecific Compiler Version Pragma](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)
- [Version Pragma | Solidity documents](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#version-pragma)
- [4.6 Unspecific compiler version pragma | Consensys Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)