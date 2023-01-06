## [L-01]
SWC-115 Authorization through tx.origin


File
```
/contracts/smart-contract-wallet/SmartAccount.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L257
```

Transaction origin
```
Use of tx.origin: "tx.origin" is useful only in very exceptional cases. 
If you use it for authentication, you usually want to replace it by "msg.sender", because otherwise any contract you call can act on your behalf.
Using "tx.origin" as a security control can lead to authorization bypass vulnerabilities. Consider using "msg.sender" unless you really know what you are doing.
```

PoC
Current code
```
address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
```

Remediation
tx.origin should not be used for authorization. Use msg.sender instead.
```
address payable receiver = refundReceiver == address(0) ? payable(msg.sender) : refundReceiver;
```


## [L-02]
SWC-115 Authorization through tx.origin


File
```
/contracts/smart-contract-wallet/SmartAccount.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L281
```

Transaction origin
```
Use of tx.origin: "tx.origin" is useful only in very exceptional cases. 
If you use it for authentication, you usually want to replace it by "msg.sender", because otherwise any contract you call can act on your behalf.
Using "tx.origin" as a security control can lead to authorization bypass vulnerabilities. Consider using "msg.sender" unless you really know what you are doing.
```

PoC
Current code
```
address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
```

Remediation
tx.origin should not be used for authorization. Use msg.sender instead.
```
address payable receiver = refundReceiver == address(0) ? payable(msg.sender) : refundReceiver;
```

## [L-03]
SWC-115 Authorization through tx.origin


File
```
/contracts/smart-contract-wallet/SmartAccount.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L511
```

Transaction origin
```
Use of tx.origin: "tx.origin" is useful only in very exceptional cases. 
If you use it for authentication, you usually want to replace it by "msg.sender", because otherwise any contract you call can act on your behalf.
Using "tx.origin" as a security control can lead to authorization bypass vulnerabilities. Consider using "msg.sender" unless you really know what you are doing.
```

PoC
Current code
```
require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
```

Remediation
tx.origin should not be used for authorization. Use msg.sender instead.
```
require(owner == hash.recover(userOp.signature) || msg.sender == address(0), "account: wrong signature");
```

## [L-04]
SWC-103 Floating Pragma


File
```
/aa-4337/core/EntryPoint.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L6
```

A floating pragma is set.
```
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. 
Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
```

PoC
Current code
```
pragma solidity ^0.8.12;
```

Remediation
remove the circumflex ^
```
pragma solidity 0.8.12;
```

## [L-05]
SWC-103 Floating Pragma


File
```
/aa-4337/core/SenderCreator.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L2
```

A floating pragma is set.
```
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. 
Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
```

PoC
Current code
```
pragma solidity ^0.8.12;
```

Remediation
remove the circumflex ^
```
pragma solidity 0.8.12;
```

## [L-06]
SWC-107 Reentrancy


File
```
/aa-4337/core/SenderCreator.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L21
```

A call to a user-supplied address is executed.
```
An external message call to an address specified by the caller is executed. 
Note that the callee account might contain arbitrary code and could re-enter any function within this contract. 
Reentering the contract in an intermediate state may lead to unexpected behaviour. 
Make sure that no state modifications are executed after this call and/or reentrancy guards are in place.
One of the major dangers of calling external contracts is that they can take over the control flow. 
In the reentrancy attack (a.k.a. recursive call attack), a malicious contract calls back into the calling contract before the first invocation of the function is finished. 
This may cause the different invocations of the function to interact in undesirable ways.
```

PoC
Current code
```
success := call(gas(), initAddress, 0, add(initCallData, 0x20), mload(initCallData), 0, 32)
```

Remediation
```
use reentrancy guard import file.
```

## [L-07]
SWC-103 Floating Pragma


File
```
/aa-4337/core/StakeManager.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L2
```

A floating pragma is set.
```
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. 
Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
```

PoC
Current code
```
pragma solidity ^0.8.12;
```

Remediation
remove the circumflex ^
```
pragma solidity 0.8.12;
```

## [L-08]
SWC-103 Floating Pragma


File
```
/aa-4337/interfaces/IPaymaster.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L2
```

A floating pragma is set.
```
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. 
Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
```

PoC
Current code
```
pragma solidity ^0.8.12;
```

Remediation
remove the circumflex ^
```
pragma solidity 0.8.12;
```

## [L-09]
SWC-103 Floating Pragma


File
```
/aa-4337/interfaces/IAggregatedAccount.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L2
```

A floating pragma is set.
```
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. 
Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
```

PoC
Current code
```
pragma solidity ^0.8.12;
```

Remediation
remove the circumflex ^
```
pragma solidity 0.8.12;
```

## [L-10]
SWC-103 Floating Pragma


File
```
/aa-4337/interfaces/IEntryPoint.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L6
```

A floating pragma is set.
```
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. 
Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
```

PoC
Current code
```
pragma solidity ^0.8.12;
```

Remediation
remove the circumflex ^
```
pragma solidity 0.8.12;
```

## [L-11]
SWC-103 Floating Pragma


File
```
/aa-4337/utils/Exec.sol
```

URL
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L2
```

A floating pragma is set.
```
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. 
Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
```

PoC
Current code
```
pragma solidity >=0.7.5 <0.9.0;
```

Remediation
use fixed number
```
// e.g. 
pragma solidity 0.8.12;
```
