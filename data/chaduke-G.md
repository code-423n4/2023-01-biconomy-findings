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
G4. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L81
Using ``userOp.sender`` can save gas.
```

function getHash(UserOperation calldata userOp)
    public pure returns (bytes32) {
        //can't use userOp.hash(), since it contains also the paymasterAndData itself.
        return keccak256(abi.encode(
                userOp.sender,
                userOp.nonce,
                keccak256(userOp.initCode),
                keccak256(userOp.callData),
                userOp.callGasLimit,
                userOp.verificationGasLimit,
                userOp.preVerificationGas,
                userOp.maxFeePerGas,
                userOp.maxPriorityFeePerGas
            ));
    }

```

G5. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L99
There is no need for this line since ``requiredPreFund`` is used in the body of the function.

G6. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L473
Adding unchecked to save gas since overflow is not possible due to previous check.
```
unchecked{
     uint256 refund = opInfo.prefund - actualGasCost;
}
```

G7. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L484-L494
The implementation can be simplified because ``min(maxFeePerGas, maxPriorityFeePerGas + block.basefee)`` is always the correct answer even when ``maxFeePerGas == maxPriorityFeePerGas``.
```
function getUserOpGasPrice(MemoryUserOp memory mUserOp) internal view returns (uint256) {
    unchecked {
        return min(mUserOp.maxFeePerGas, mUserOp.maxPriorityFeePerGas + block.basefee);
    }
    
```

G8. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L336
Adding unchecked to save gas since overflow is not possible due to previous check.
```
senderInfo.deposit = uint112(deposit - requiredPrefund);

```

G9. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L362
Adding unchecked to save gas since overflow is not possible due to previous check.
```

 paymasterInfo.deposit = uint112(deposit - requiredPreFund);

```
