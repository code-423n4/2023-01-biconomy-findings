## MISSING EVENTS ON CRITICAL OPERATIONS
Critical operations not triggering events will make it difficult to review the correct behavior of the contracts. Users and blockchain monitoring systems will not be able to easily detect suspicious behaviors without events.

Here is 1 instance found.

[VerifyingSingletonPaymaster.sol#L65](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65)

## TYPO ERRORS
[SmartAccount.sol#L74](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L74)

```
     @ an
     * @notice Throws if the sender is not an the owner.
```
[SmartAccount.sol#L320](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L320)

```
    @ send
                // This check is not completely accurate, since it is possible that more signatures than the threshold are send.
```
[SmartAccount.sol#L382](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L382)

[SmartAccount.sol#L382](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L382)

```
    @ Fas
    /// @param targetTxGas Fas that should be used for the safe transaction.
```
[SignatureDecoder.sol#L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol#L4)

```
    @ a
/// @title SignatureDecoder - Decodes signatures that a encoded as bytes
```
## TODO
Open TODO can point to an architecture or programming issue needing to be resolved. It is recommended resolving them before deploying.

Here are the 2 instances found.

[SmartAccountNoAuth.sol#L44](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L44)

```
    // todo? rename wallet to account
```
[EntryPoint.sol#L255](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255)

```
        // TODO: copy logic of gasPrice?
```
## SETTING TO DEFAULT VALUES
As documented in the link:

https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=delete#delete

It is recommended using `delete` whenever there is a need to set state variable to its default value, which has a good side effect of saving gas. 

Here is 1 instance found.

[ModuleManager.sol#L52](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L52)

```
        modules[module] = address(0);
```
## USE UINT256 INSTEAD OF 'UINT`
For explicitness reason, it is recommended replacing all instances of `uint` with `uint256`. 

Here are the 7 instances of `uint` used in the code base.

[SmartAccountFactory.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol)

```
33:    function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address proxy){
34:        bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
35:        bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));

54:        bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));

68:    function getAddressForCounterfactualWallet(address _owner, uint _index) external view returns (address _wallet) {
69:       bytes memory code = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));

72:        _wallet = address(uint160(uint(hash)));
```
## SINGLE POINT OF FAILURE
Contract owner possesses numerous privileges that could prove disastrous if the private key was compromised. If the key was lost/unrecoverable, all needed system parameter updates would be halted.

An established owner is generally prone to target attack, especially given the insufficient protection on sensitive owner private keys. The concentration of privileges creates a single point of failure, leading to transfer of ownership and malicious reset of setter function parameters.

The following measures are recommended.
1) Split privileges using multisig option making sure that no one address has excessive ownership of the system.
2) Clearly document the functions and implementations the owner can change.
3) Document the risks associated with privileged users and single points of failure.
4) Make sure that users are aware of all the risks associated with the system.

## CONTRACT EXISTENCE CHECK
Low-level calls do not check for contract existence. They are known to return success even though no function call has been executed when involving contract that may not have been deployed or have been self-destructed. 

Here are 4 of the instances found.

[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

```
261:            (bool success,) = receiver.call{value: payment}("");

285:            (bool success,) = receiver.call{value: payment}("");

451:        (bool success,) = dest.call{value:amount}("");

478:        (bool success, bytes memory result) = target.call{value : value}(data);
```