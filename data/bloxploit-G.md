# Gas Optimisation Report

## Require,Asset and Revert string size should be small


### Require

```bash
./paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:49:        require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");

```

Occurance:[VerifyingSingletonPaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol)


### Revert

```bash
./paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:42:        revert("Deposit must be for a paymasterId. Use depositFor");
./paymasters/BasePaymaster.sol:52:        revert("must override");
```

Occurance:[VerifyingSingletonPaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol),[BasePaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol)


## Empty block should be removed or made to revert something.

```bash
./SmartAccount.sol:550:    receive() external payable {}
./aa-4337/core/EntryPoint.sol:119:                try aggregator.validateSignatures(ops, opa.signature) {}
./aa-4337/core/EntryPoint.sol:458:                    try IPaymaster(paymaster).postOp{gas : mUserOp.verificationGasLimit}(mode, context, actualGasCost) {}
./SmartAccountNoAuth.sol:540:    receive() external payable {}
```

Occurance:[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol),[Entrypoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol),[SmartAccountNoAuth.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol)



## abi.encode() is less efficient then abi.encodepacked()


```bash

./SmartAccount.sol:136:        return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
./SmartAccount.sol:431:                abi.encode(
./aa-4337/core/EntryPoint.sol:197:        return keccak256(abi.encode(userOp.hash(), address(this), block.chainid));
./SmartAccountNoAuth.sol:136:        return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
./SmartAccountNoAuth.sol:421:                abi.encode(
./paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:80:        return keccak256(abi.encode(
./paymasters/PaymasterHelpers.sol:28:        return abi.encode(data.paymasterId);

```


Occurance:[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol),[Entrypoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol),[SmartAccountNoAuth.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol),[VerifyingSingletonPaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol)[PaymasterHelpers.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol)




## Use selfbalance() instead of address(this).balance when getting your contract’s balance of ETH to save gas.

``` bash

./SmartAccount.sol:519:        return entryPoint().balanceOf(address(this));
./aa-4337/core/BasePaymaster.sol:83:        return entryPoint.balanceOf(address(this));
./SmartAccountNoAuth.sol:509:        return entryPoint().balanceOf(address(this));
./paymasters/BasePaymaster.sol:83:        return entryPoint.balanceOf(address(this));

```

Occurance:[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol),[/aa-4337/core/BasePaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol),[SmartAccountNoAuth.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol),[/paymasters/BasePaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol)

