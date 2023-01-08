## \[L-01\] NO TRANSFER OWNERSHIP PATTERN

Recommend considering implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.



### ./contracts/smart-contract-wallet/SmartAccount.sol : 
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109-L114
```solidity
109:    function setOwner(address _newOwner) external mixedAuth {
110:        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
111:        address oldOwner = owner;
112:        owner = _newOwner;
113:        emit EOAChanged(address(this), oldOwner, _newOwner);
114:    }

```