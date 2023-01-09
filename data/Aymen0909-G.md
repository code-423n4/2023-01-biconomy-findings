# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1   | Use `unchecked` blocks to save gas  | 2 |
| 2   | Redundant check in `init` function should be removed | 1 |
| 3   | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  | 3 |
| 4   | `require()` strings longer than 32 bytes cost extra gas  |  16 |
| 5   | Splitting `require()` statements that uses && saves gas  |  3 |
| 6   | `public` functions not called by the contract should be declared `external` instead | 17 |
| 7   | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops | 5 |


## Findings

### 1- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 2 instances of this issue :

File: StakeManager.sol [Line 118](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L118)
```
info.deposit = uint112(info.deposit - withdrawAmount);
```

The above operation cannot underflow due to the check :

[require(withdrawAmount <= info.deposit, "Withdraw amount too large");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L117) 

It should be marked as `unchecked`. 

File: VerifyingSingletonPaymaster.sol [Line 58](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L58)
```
paymasterIdBalances[msg.sender] -= amount;
```

The above operation cannot underflow due to the check :

[require(amount <= currentBalance, "Insufficient amount to withdraw");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L57) 

It should be marked as `unchecked`. 


### 2- Redundant check in `init` function should be removed :

In the `init` function from the `SmartAccount` the following check is redundant :

File: SmartAccount.sol [Line 174](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L174)
```
if (_handler != address(0)) internalSetFallbackHandler(_handler);
```

Because the check [line 171](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171) ensures that we always have `_handler != address(0)` and so the `internalSetFallbackHandler` should be called immediatly and the if check should be removed to save gas.

### 3- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 3 instances of this issue:

File: VerifyingSingletonPaymaster.sol [Line 51](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L51)
```
paymasterIdBalances[paymasterId] += msg.value;
```

File: VerifyingSingletonPaymaster.sol [Line 58](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L58)
```
paymasterIdBalances[msg.sender] -= amount;
```

File: VerifyingSingletonPaymaster.sol [Line 128](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L128)
```
paymasterIdBalances[extractedPaymasterId] -= actualGasCost;
```

### 4- `require()` strings longer than 32 bytes cost extra gas :

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

There are 16 instances of this issue :

```
File: SmartAccount.sol

77      require(msg.sender == owner, "Smart Account:: Sender is not authorized");
110     require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
128     require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");

File: SmartAccountFactory.sol

18      require(_baseImpl != address(0), "base wallet address can not be zero");

File: SmartAccountNoAuth.sol

77      require(msg.sender == owner, "Smart Account:: Sender is not authorized");
110     require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
128     require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
440     require(dest != address(0), "this action will burn your funds");

File: VerifyingSingletonPaymaster.sol

36      require(address(_entryPoint) != address(0), "VerifyingPaymaster: Entrypoint can not be zero address");
37      require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");
49      require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");
50      require(paymasterId != address(0), "Paymaster Id can not be zero address");
66      require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
107     require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
108     require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");
109     require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");
```

### 5- Splitting `require()` statements that uses && saves gas :

There are 3 instances of this issue :

```
File: base/ModuleManager.sol

34      require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
49      require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
68      require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```

### 6- `public` functions not called by the contract should be declared `external` instead :

The `public` functions that are not called inside the contract should be declared `external` instead to save gas.

There are 17 instances of this issue:

```
File: SmartAccount.sol

155      function getNonce(uint256 batchId) public
166      function init(address _owner, address _entryPointAddress, address _handler) public  
389      function execTransaction(Transaction memory _tx, uint256 batchId, FeeRefund memory refundInfo, bytes memory signatures) public
518      function getDeposit() public 
525      function addDeposit() public 
536      function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public 

File:  SmartAccountFactory.sol

33      function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public 
53      function deployWallet(address _owner, address _entryPoint, address _handler) public  

File: base/FallbackManager.sol

26      function setFallbackHandler(address handler) public 

File: base/ModuleManager.sol

32      function enableModule(address module) public 
47      function disableModule(address prevModule, address module) public 
80      function execTransactionFromModuleReturnData(address to, uint256 value, bytes memory data, Enum.Operation operation) public
105     function isModuleEnabled(address module) public view returns (bool) 

File: libs/MultiSend.sol

26      function multiSend(bytes memory transactions) public 

File: libs/MultiSendCallOnly.sol

21      function multiSend(bytes memory transactions) public 

File: paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

48      function depositFor(address paymasterId) public 
55      function withdrawTo(address payable withdrawAddress, uint256 amount) public
```

### 7- `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 5 instances of this issue:
 
```
File: EntryPoint.sol

100     for (uint256 i = 0; i < opasLen; i++)
107     for (uint256 a = 0; a < opasLen; a++) 
112     for (uint256 i = 0; i < opslen; i++)
128     for (uint256 a = 0; a < opasLen; a++) 
134     for (uint256 i = 0; i < opslen; i++)
```