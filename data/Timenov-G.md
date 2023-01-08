# Biconomy gas optimization report by Timenov

## Summary
G-01 Using `> 0` costs more gas than using `!= 0`
G-02 Using `i++` costs more gas than using `++i`. Also can add `unchecked { ++i; }` in for loops where overflow is not possible
G-03 Using `x += y` costs more gas than using `x = x + y`

### [G-01] Using `> 0` costs more gas than using `!= 0`
*There are 12 instances of this issue.*

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

174: if (callData.length > 0)
178: if (result.length > 0)
212: if (paymasterAndData.length > 0)
452: if (context.length > 0)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

61: require(_unstakeDelaySec > 0, "must specify unstake delay")
64: require(stake > 0, "no stake specified")
99: require(stake > 0, "No stake to withdraw")
100: require(info.withdrawTime > 0, "must call unlockStake() first")
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

```solidity
File: contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol

31: if (codeSize > 0)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol

```solidity
File: contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccountFactory.sol

30: if (codeSize > 0)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccountFactory.sol

```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol

236: if (refundInfo.gasPrice > 0)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```solidity
File: contracts/smart-contract-wallet/SmartAccountNoAuth.sol

if (refundInfo.gasPrice > 0)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol

### [G-02] Using `i++` costs more gas than using `++i`. Also can add `unchecked { ++i; }` in for loops where overflow is not possible
*There are 17 instances of this issue.*

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

74: for (uint256 i = 0; i < opslen; i++)
80: for (uint256 i = 0; i < opslen; i++)
100: for (uint256 i = 0; i < opasLen; i++)
107: for (uint256 a = 0; a < opasLen; a++)
112: for (uint256 i = 0; i < opslen; i++)
114: opIndex++;
128: for (uint256 a = 0; a < opasLen; a++)
134: for (uint256 i = 0; i < opslen; i++)
136: opIndex++;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```solidity
File: contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol

71: for (uint256 i = 0; i < dest.length; i++)
104: require(_nonce++ == userOp.nonce, "account: invalid nonce")
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol

```solidity
File: contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol

19: for (uint i = 0; i < userOps.length; i++)
39: for (uint i = 0; i < userOps.length; i++)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol

```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol

216: nonces[batchId]++;
502: require(nonces[0]++ == userOp.nonce, "account: invalid nonce");
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```solidity
File: contracts/smart-contract-wallet/SmartAccountNoAuth.sol

216: nonces[batchId]++;
492: require(nonces[0]++ == userOp.nonce, "account: invalid nonce");
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol

### [G-03] Using x += y costs more gas than using x = x + y
*There are 9 instances of this issue.*

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

81: collected += _executeUserOp(i, ops[i], opInfos[i]);
101: totalOps += opsPerAggregator[i].userOps.length;
468: actualGas += preGas - gasleft();
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```solidity
File: contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol

67: balances[token][account] += amount;
103: balances[token][msg.sender] -= amount;
160: balances[token][account] -= actualTokenCost;
162: balances[token][owner()] += actualTokenCost;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol

```solidity
File: contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol

21: sum += nonce;
40: sum += userOps[i].nonce;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol