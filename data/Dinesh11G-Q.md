### Unspecific Compiler Version Pragma

#### Impact
Issue Information: 
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Example
🤦 Bad:

pragma solidity ^0.8.0;
🚀 Good:

pragma solidity 0.8.4;

#### Findings:
```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol::2 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol::2 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::6 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol::2 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol::2 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol::2 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol::2 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol::6 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol::2 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol::2 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol::2 => pragma solidity ^0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol::2 => pragma solidity ^0.8.12;
```
#### Tools used
Manual code review on vs code