

## [L-01] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol::83 => * @return deadline the last block timestamp this operation is valid, or zero if it is valid indefinitely.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol::84 => *      Note that the validation code cannot use block.timestamp (or block.number) directly.
```

## [L-02] Unused receive()/fallback() function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::550 => receive() external payable {}
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::540 => receive() external payable {}
```

## [L-03] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::347 => _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::34 => bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::70 => bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::71 => bytes32 hash = keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, keccak256(code)));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::342 => _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);
```

## [L-04] require() should be used instead of assert()

require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```

## [L-05] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::20 => function setupModules(address to, bytes memory data) internal {
```

## [L-06] Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

__gap is empty reserved space in storage that is recommended to be put in place in upgradeable contracts. It allows new state variables to be added in the future without compromising the storage compatibility with existing deployments

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::7 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::7 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
```

## [L-07] Implementation contract may not be initialized

Implementation contract does not have a constructor with the initializer modifier therefore may be uninitialized. Implementation contracts should be initialized to avoid potential griefs or exploits.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::7 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::7 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
```

## [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol::2 => pragma solidity 0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/Proxy.sol::2 => pragma solidity 0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::2 => pragma solidity 0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::2 => pragma solidity 0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::2 => pragma solidity 0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol::2 => pragma solidity 0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol::2 => pragma solidity 0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::2 => pragma solidity 0.8.12;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::2 => pragma solidity 0.8.12;
```

## [N-02] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::64 => event ImplementationUpdated(address _scw, string version, address newImplementation);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::65 => event EntryPointChanged(address oldEntryPoint, address newEntryPoint);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::67 => event WalletHandlePayment(bytes32 txHash, uint256 payment);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::64 => event ImplementationUpdated(address _scw, string version, address newImplementation);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::65 => event EntryPointChanged(address oldEntryPoint, address newEntryPoint);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::67 => event WalletHandlePayment(bytes32 txHash, uint256 payment);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol::9 => event ExecutionFailure(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol::10 => event ExecutionSuccess(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol::9 => event ChangedFallbackHandler(address handler);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::11 => event EnabledModule(address module);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::12 => event DisabledModule(address module);
```

## [N-03] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::20 => function setupModules(address to, bytes memory data) internal {
```

## [N-04] Non-assembly method available

assembly{ id := chainid() } => uint256 id = block.chainid
assembly { size := extcodesize() } => uint256 size = address().code.length

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::144 => id := chainid()
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::144 => id := chainid()
```

## [N-05] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::46 => //     "AccountTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::239 => payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::357 => ///      Since the `estimateGas` function includes refunds, call this method to get an estimated of the costs that are deducted from the safe with `execTransaction`
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::489 => function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::46 => //     "WalletTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::239 => payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::352 => ///      Since the `estimateGas` function includes refunds, call this method to get an estimated of the costs that are deducted from the safe with `execTransaction`
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::479 => function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {
```

