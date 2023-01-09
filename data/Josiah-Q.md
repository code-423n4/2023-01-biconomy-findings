## UNUTILIZED LIBRARY
Throughout the code bases, there are instances where functions in Math.sol are re-implemented instead of getting them utilized via library import.

[Math.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol)

```
19:    function max(uint256 a, uint256 b) internal pure returns (uint256) {

26:    function min(uint256 a, uint256 b) internal pure returns (uint256) {
```
[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

```
181:    function max(uint256 a, uint256 b) internal pure returns (uint256) {

224:        require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
```
[UserOperation.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol)

```
53:        return min(maxFeePerGas, maxPriorityFeePerGas + block.basefee);

77    function min(uint256 a, uint256 b) internal pure returns (uint256) {
```
[EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)

```
492:        return min(maxFeePerGas, maxPriorityFeePerGas + block.basefee);

496:    function min(uint256 a, uint256 b) internal pure returns (uint256) {
```
## MISSING EVENTS ON CRITICAL OPERATIONS
Critical operations not triggering events will make it difficult to review the correct behavior of the contracts. Users and blockchain monitoring systems will not be able to easily detect suspicious behaviors without events.

Here is 1 instance found.

[VerifyingSingletonPaymaster.sol#L65](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65)

```
    function setSigner( address _newVerifyingSigner) external onlyOwner{
```
## REPEATED CHECK
In `init()` of `SmartAccount.sol`, the zero address check on `_handler` was carried out twice. It is recommended removing the if statement that is associated with the redundant check.

[SmartAccount.sol#L171-L174](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171-L174)
 
```
        require(_handler != address(0), "Invalid Entrypoint");
        owner = _owner;
        _entryPoint =  IEntryPoint(payable(_entryPointAddress));
        if (_handler != address(0)) internalSetFallbackHandler(_handler);
```
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

```
    @ Fas
    /// @param targetTxGas Fas that should be used for the safe transaction.
```
[SignatureDecoder.sol#L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol#L4)

```
    @ a
/// @title SignatureDecoder - Decodes signatures that a encoded as bytes
```
## EXCLUDED STRUCT VARIABLE
It is found that `tokenGasPriceFactor` of the struct `FeeRefund` has not been included in the concatenating abi.encode logic of `encodeTransactionData()`. It is recommended documenting the reason for such exclusion so users and developers will be better able to analyze this function.

[BaseSmartAccount.sol#L20-L26](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L20-L26)

```
struct FeeRefund {
        uint256 baseGas;
        uint256 gasPrice; //gasPrice or tokenGasPrice
        uint256 tokenGasPriceFactor;
        address gasToken;
        address payable refundReceiver;
    }
```
[SmartAccount.sol#L424-L446](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L424-L446)

```
    function encodeTransactionData(
        Transaction memory _tx,
        FeeRefund memory refundInfo,
        uint256 _nonce
    ) public view returns (bytes memory) {
        bytes32 safeTxHash =
            keccak256(
                abi.encode(
                    ACCOUNT_TX_TYPEHASH,
                    _tx.to,
                    _tx.value,
                    keccak256(_tx.data),
                    _tx.operation,
                    _tx.targetTxGas,
                    refundInfo.baseGas,
                    refundInfo.gasPrice,
                    refundInfo.gasToken,
                    refundInfo.refundReceiver,
                    _nonce
                )
            );
        return abi.encodePacked(bytes1(0x19), bytes1(0x01), domainSeparator(), safeTxHash);
    }
```
## TODO
Open TODO can point to an architecture or programming issue needing to be resolved. It is recommended resolving them before deploying.

Here are the 2 instances found.

[SmartAccountNoAuth.sol#L44](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L44) (Note: Out of scope but included here for informational purpose)

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

Here are the 5 instances found.

[ModuleManager.sol#L52](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L52)

```
        modules[module] = address(0);
```
[StakeManager.sol#L86](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L86)

```
86:        info.staked = false;

102:        info.unstakeDelaySec = 0;
103:        info.withdrawTime = 0;
104:        info.stake = 0;
```
## UNNECESSARY CASTING
In `checkSignatures()` of `SmartAccount.sol`, casting 1 to uint256, i.e. `uint256(1)`, and then making it a factor in a product is neither necessary and needed. First off, the numeral 1 is already an unsigned integer. Secondly, the product of 1 * 65 is equivalent to 65.

[SmartAccount.sol#L322](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L322)

```
                require(uint256(s) >= uint256(1) * 65, "BSA021");
```
## USE UINT256 INSTEAD OF UINT
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
## WHILE LOOP SKIPPED LEADS TO FUTILE RETURNED VARIABLES
In `getModulesPaginated()` of `ModuleManager.sol`, the while logic would be bypassed if `modules[start]` was mapped to zero address, and then the function would run to return an empty array and a zero address.

It is recommended isolating `currentModule != address(0x0)` to a require statement before the while loop. That way, the function would revert if the condition wasn't met and skip the rest of the unneeded executions.

[ModuleManager.sol#L121](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L121)

```
        while (currentModule != address(0x0) && currentModule != SENTINEL_MODULES && moduleCount < pageSize) {
```
## NOMENCLATURE
Interfaces should be named beginning with the letter `I` while contracts, abstract contracts, and libraries should avoid having their names prepended with `I`.

Here are the 4 instances found.

[ERC1155TokenReceiver.sol#L7](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L7)

```
interface ERC1155TokenReceiver {
```
[ERC721TokenReceiver.sol#L5](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol#L5)

```
interface ERC721TokenReceiver {
```
[ERC777TokensRecipient.sol#L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol#L4)

```
interface ERC777TokensRecipient {
```
[ISignatureValidator.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol)

```
4: contract ISignatureValidatorConstants {

9: abstract contract ISignatureValidator is ISignatureValidatorConstants {
```
## STATICCALL 
As denoted in [MEDIUM](https://medium.com/blockchannel/state-specifiers-and-staticcall-d50d5b2e4920):

"The Byzantium network upgrade ... will add a STATICCALL opcode that enforces read-only calls at runtime. Solidity only implements the STATICCALL opcode in its assembly language ..."

Consider commenting and/or documenting in the protocol how [`staticcall()`](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L19-L27) is gaslessly engaged on reverting methods that are used for gas estimations in SmartAccount.sol as well as simulations in EntryPoint.sol.

## MISSING NATSPEC
As documented in the link below:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

It is recommended using a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more.

Here are some of the instances found.

[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

```
127:    function updateEntryPoint(address _newEntryPoint) external mixedAuth { // Missing full spec

135:    function domainSeparator() public view returns (bytes32) { // Missing full spec

192:    function execTransaction( // Missing @return

247:    function handlePayment( // Missing full spec

271:    function handlePaymentRevert( // Missing full spec

302:    function checkSignatures( // Missing @param data
```