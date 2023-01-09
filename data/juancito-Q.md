# QA Report

## Summary

### Low Issues

|       | Issue                                               | Instances |
| ----- | :-------------------------------------------------- | :-------: |
| [L-1] | Missing checks for `address(0)` in withdraw methods |     7     |
| [L-2] | Unresolved `TODO` and `review` comments             |    12     |
| [L-3] | Unspecific compiler version pragma                  |    11     |
| [L-4] | Missing event emit on `deployWallet`                |     1     |

### Non Critical Issues

|        | Issue                                                       | Instances |
| ------ | :---------------------------------------------------------- | :-------: |
| [NC-1] | Commented code lines                                        |    12     |
| [NC-2] | Test coverage improvement                                   |    18     |
| [NC-3] | Misleading error messages                                   |     2     |
| [NC-4] | Typos                                                       |     4     |
| [NC-5] | Constants should be defined rather than using magic numbers |     1     |

## Low Issues

### [L-1] Missing checks for `address(0)` in withdraw methods

Consider checking for `address(0)` to prevent any loss of funds.

_Instances 7_:

```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol

537:    entryPoint().withdrawTo(withdrawAddress, amount);

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

```solidity
File: contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

68:     entryPoint.withdrawTo(withdrawAddress, amount);

100:    entryPoint.withdrawStake(withdrawAddress);

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol)

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

106:    (bool success,) = withdrawAddress.call{value : stake}("");
120:    (bool success,) = withdrawAddress.call{value : withdrawAmount}("");

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol)

```solidity
File: contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

100:    entryPoint.withdrawStake(withdrawAddress);

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol)

```solidity
File: contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

59:    entryPoint.withdrawTo(withdrawAddress, amount);

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol)

### [L-2] Unresolved `TODO` and `review` comments

Open `TODO` and `review` comments indicate code that has to be addressed before production deployment.

_Instances 12_:

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

255:    // TODO: copy logic of gasPrice?

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)

```solidity
File: contracts/smart-contract-wallet/BaseSmartAccount.sol

59:     // review virtual

86:     // Review if we need to make view function

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol)

```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol

44:     // review? if rename wallet to account is must

58:     // review

68:     // nice to have
69:     // event SmartAccountInitialized(IEntryPoint indexed entryPoint, address indexed owner);

149:    //@review getNonce specific to EntryPoint requirements

313:    //review

487:    //@review

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

```solidity
File: contracts/smart-contract-wallet/SmartAccountFactory.sol

14:     // review need and impact of this update wallet -> account

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol)

```solidity
File: contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol

16:     // Review for sig collision and HAL-04 report i

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol)

```solidity
File: contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

15:     //@review

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol)

### [L-3] Unspecific compiler version pragma

For many files the compiler version pragma is the unspecific `^0.8.12`. Consider pinning a concrete compiler version to prevent a known vulnerable version to be accidentally selected.

_Instances 11_:

```solidity
File: contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

2:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol)

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

6:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol

2:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol)

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

2:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol)

```solidity
File: contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol

2:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol)

```solidity
File: contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol

2:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol)

```solidity
File: contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol

6:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol)

```solidity
File: contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol

2:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol)

```solidity
File: contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol

2:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol)

```solidity
File: contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol

2:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol)

```solidity
File: contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

2:      pragma solidity ^0.8.12;

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol)

### [L-4] Missing event emit on `deployWallet`

The `deployWallet` method is not emiting an event when an account is created, as `deployCounterFactualWallet` does. Accounts created with `deployWallet` will be missing on logs, deriving in inconsistent behavior on off-chain tools. Consider emiting an event on `deployWallet`.

_Instances 1_:

```solidity
File: contracts/smart-contract-wallet/SmartAccountFactory.sol

53:     function deployWallet(address _owner, address _entryPoint, address _handler) public returns(address proxy){
54:         bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
55:         // solhint-disable-next-line no-inline-assembly
56:         assembly {
57:             proxy := create(0x0, add(0x20, deploymentData), mload(deploymentData))
58:         }
59:         BaseSmartAccount(proxy).init(_owner, _entryPoint, _handler);
60:         isAccountExist[proxy] = true;
61:     }

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol)

## Non Critical Issues

### [NC-1] Commented code lines

Remove commented code lines for code clarity.

_Instances (12)_:

```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol

53:     // uint96 private _nonce; //changed to 2D nonce below

201:    //console.log("init %s", 21000 + msg.data.length * 8);

235:    // uint256 extraGas;

237:    //console.log("sent %s", startGas - gasleft());

238:    // extraGas = gasleft();

242:    // extraGas = extraGas - gasleft();

243:    //console.log("extra gas %s ", extraGas);

255:    // uint256 startGas = gasleft();

267:    // uint256 requiredGas = startGas - gasleft();

268:    //console.log("hp %s", requiredGas);

292:    //console.log("hpr %s", requiredGas);

293:    // Convert response to string and return via error message

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

### [NC-2] Test coverage improvement

Test coverage seems to be limited. Consider covering all methods and functions that can directly be accessed including potential security-relevant and edge-cases.

_Instances (18)_:

```bash
---------------------------------------------|----------|----------|----------|----------|----------------|
File                                         |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
---------------------------------------------|----------|----------|----------|----------|----------------|
 smart-contract-wallet/                      |     35.1 |       16 |    29.87 |    35.79 |                |
  BaseSmartAccount.sol                       |        0 |        0 |        0 |        0 |... 107,108,109 |
  SmartAccount.sol                           |    63.92 |     32.2 |    52.94 |    64.23 |... 528,537,546 |
  SmartAccountFactory.sol                    |    81.82 |       50 |       75 |    76.47 |    54,56,59,60 |
 smart-contract-wallet/aa-4337/core/         |    98.46 |    81.15 |    93.18 |    97.16 |                |
  EntryPoint.sol                             |      100 |    88.16 |      100 |    98.09 |373,421,463,471 |
  StakeManager.sol                           |      100 |    76.92 |      100 |      100 |                |
 smart-contract-wallet/aa-4337/interfaces/   |       80 |       25 |       80 |    84.62 |                |
  UserOperation.sol                          |       80 |       25 |       80 |    84.62 |          53,78 |
 smart-contract-wallet/aa-4337/utils/        |        0 |        0 |        0 |        0 |                |
  Exec.sol                                   |        0 |        0 |        0 |        0 |... 52,58,59,60 |
 smart-contract-wallet/base/                 |       24 |    17.86 |    27.27 |    23.08 |                |
  Executor.sol                               |       75 |       75 |      100 |      100 |                |
  FallbackManager.sol                        |       25 |        0 |    33.33 |    33.33 |    27,28,33,35 |
  ModuleManager.sol                          |    11.76 |     9.09 |    14.29 |    10.34 |... 124,126,129 |
 smart-contract-wallet/common/               |       25 |        0 |    33.33 |    33.33 |                |
  Singleton.sol                              |        0 |      100 |        0 |        0 |       13,15,22 |
 smart-contract-wallet/handler/              |        0 |        0 |        0 |        0 |                |
  DefaultCallbackHandler.sol                 |        0 |        0 |        0 |        0 |    22,32,41,56 |
 smart-contract-wallet/libs/                 |     1.45 |     1.47 |    13.64 |      2.7 |                |
  LibAddress.sol                             |        0 |      100 |        0 |        0 |       12,14,15 |
  Math.sol                                   |        0 |        0 |        0 |        0 |... 340,341,342 |
  MultiSend.sol                              |      100 |       50 |      100 |      100 |                |
 smart-contract-wallet/paymasters/           |    23.53 |     8.33 |    21.43 |    26.32 |                |
  BasePaymaster.sol                          |     9.09 |     8.33 |    18.18 |    15.38 |... ,91,100,105 |
  PaymasterHelpers.sol                       |       50 |      100 |    33.33 |       50 |       28,44,45 |
 smart-contract-wallet/paymasters/verifying/ |       45 |    27.27 |     37.5 |    40.74 |                |
  singleton/VerifyingSingletonPaymaster.sol  |       45 |    27.27 |     37.5 |    40.74 |... 126,127,128 |
---------------------------------------------|----------|----------|----------|----------|----------------|
```

### [NC-3] Misleading error messages

There are `require` statements with misleading error messages to what they are checking. Consider reviewing and updating them accordingly.

_Instances (2)_:

```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol

167:    require(owner == address(0), "Already initialized");

171:    require(_handler != address(0), "Invalid Entrypoint");

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

### [NC-4] Typos

_Instances (4)_:

```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol

/// @audit: from "Seperators" to "Separators"
38:     // Domain Seperators

/// @audit: from "is not an the owner" to "is not the owner"
74:     // * @notice Throws if the sender is not an the owner.

/// @audit: from "send" to "sent"
223:    // We also include the 1/64 in the check that is not send along with a call to counteract potential shortings because of EIP-150

/// @audit: from "send" to "sent"
320:    // This check is not completely accurate, since it is possible that more signatures than the threshold are send.

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

### [NC-5] Constants should be defined rather than using magic numbers

_Instances 1_:

```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol

/// @audit: magic numbers: `1`, `65`
322:    require(uint256(s) >= uint256(1) * 65, "BSA021");

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)
