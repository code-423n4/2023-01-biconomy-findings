G1. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L58
Adding unchecked to save gas since overflow is not possible due to previous check.
```
unchecked{
     paymasterIdBalances[msg.sender] -= amount;
}

```

G2. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L118
Adding unchecked to save gas since overflow is not possible due to previous check.
```
unchecked{
 info.deposit = uint112(info.deposit - withdrawAmount);
 
}
```

G3. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L58
Adding unchecked to save gas since overflow is not possible due to previous check.
```
unchecked{
     paymasterIdBalances[msg.sender] -= amount;
}
```
