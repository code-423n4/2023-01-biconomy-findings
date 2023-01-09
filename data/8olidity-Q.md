## There is no need to specify gas, call() is originally unlimited gas


There is no need to specify gas as type(uint256).max in call()
```solidity
File: scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

    function _payPrefund(uint256 missingAccountFunds) internal virtual {
        if (missingAccountFunds != 0) {
            (bool success,) = payable(msg.sender).call{value : missingAccountFunds, gas : type(uint256).max}(""); //@audit 
            (success);
            //ignore failure (its EntryPoint's job to verify, not account.)
        }
    }
```

## Transfer permissions require two-step authentication
Transferring the owner's permission requires the newowner address to accept it, rather than directly transferring the permission to the new address


```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

    function setOwner(address _newOwner) external mixedAuth { //@audit  
        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
        address oldOwner = owner;
        owner = _newOwner;
        emit EOAChanged(address(this), oldOwner, _newOwner);
    }
```

## updateImplementation() should check if the `_implementation address` is 0

In the updateImplementation() function, directly assign the incoming _implementation address to _IMPLEMENTATION_SLOT without any judgment

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

    function updateImplementation(address _implementation) external mixedAuth {
        require(_implementation.isContract(), "INVALID_IMPLEMENTATION"); //@audit  
        _setImplementation(_implementation);
        // EOA + Version tracking
        emit ImplementationUpdated(address(this), VERSION, _implementation);
    }
```



## `_validateSignature()` return deadline is always 0



```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)
    internal override virtual returns (uint256 deadline) {
        bytes32 hash = userOpHash.toEthSignedMessageHash();
        //ignore signature mismatch of from==ZERO_ADDRESS (for eth_callUserOp validation purposes)
        // solhint-disable-next-line avoid-tx-origin
        require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
        return 0; //@audit  
    }
```

