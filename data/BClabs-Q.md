1. **Function duplicate**:

There are 2 functions that implmenet the same logic- They both accept batchId and return nonces[batchId].

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L155-L159
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L97-L99

2. **Wrong error message**:

Should be invalid _handler not Invalid Entrypoint.

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171

3. **Address.isContract does not work as expected when called from constructor**: 

Address.isContract(paymasterId) can be fooled when calling a function from the contract's constructor. Since isContract checks contract size, which is at the time of constructor 0, it will return that the address is not contract.

```
contract Attack {
    constructor(address contractAddress) {
        VerifyingSingletonPaymaster(contractAddress).depositFor{value: 1}(address(this));
    }
}
```

To be sure paymasterId is not a contract add require tx.origin == msg.sender.
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L49


4. **Abstract contracts should have some functions unimplemented**

Since StakeManager is a complete contract, it should not be marked as abstract.

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L13