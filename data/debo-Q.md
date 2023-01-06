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