### 1st BUG
Don't Initialize Variables with Default Value

#### Impact
Issue Information: 
Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### Findings:
```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::234 => uint256 payment = 0;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::305 => uint256 i = 0;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::458 => for (uint i = 0; i < dest.length;) { uint256 missingAccountFunds = 0;
```
#### Tools used
Manual code review on vs code

### 2nd BUG
Cache Array Length Outside of Loop

#### Impact
Issue Information: 
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol::64 => if (userOp.initCode.length == 0) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::201 => //console.log("init %s", 21000 + msg.data.length * 8);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::324 => // Check that signature data pointer (s) is in bounds (points to the length of data -> 32 bytes)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::325 => require(uint256(s) + 32 <= signatures.length, "BSA022");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::327 => // Check if the contract signature is in bounds: start of data is s + 32 and end is start + signature length
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::333 => require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::467 => require(dest.length == func.length, "wrong array lengths");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::201 => //console.log("init %s", 21000 + msg.data.length * 8);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::319 => // Check that signature data pointer (s) is in bounds (points to the length of data -> 32 bytes)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::320 => require(uint256(s) + 32 <= signatures.length, "BSA022");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::322 => // Check if the contract signature is in bounds: start of data is s + 32 and end is start + signature length
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::328 => require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::457 => require(dest.length == func.length, "wrong array lengths");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::458 => for (uint i = 0; i < dest.length;) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol::39 => if (userOp.initCode.length == 0) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::70 => uint256 opslen = ops.length;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::98 => uint256 opasLen = opsPerAggregator.length;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::101 => totalOps += opsPerAggregator[i].userOps.length;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::111 => uint256 opslen = ops.length;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::132 => uint256 opslen = ops.length;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::174 => if (callData.length > 0) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::178 => if (result.length > 0) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::212 => if (paymasterAndData.length > 0) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::213 => require(paymasterAndData.length >= 20, "AA93 invalid paymasterAndData");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::237 => address factory = initCode.length >= 20 ? address(bytes20(initCode[0 : 20])) : address(0);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::262 => if (initCode.length != 0) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::264 => if (sender.code.length != 0) revert FailedOp(opIndex, address(0), "AA10 sender already constructed");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::268 => if (sender1.code.length == 0) revert FailedOp(opIndex, address(0), "AA15 initCode must create sender");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::299 => if (sender.code.length == 0) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::303 => if (mUserOp.paymaster != address(0) && mUserOp.paymaster.code.length == 0) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::452 => if (context.length > 0) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol::40 => * An event emitted if the UserOperation "callData" reverted with non-zero length
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol::22 => *  zero length to signify postOp is not required.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::37 => @param _ids       An array containing ids of each token being transferred (order and length must match _values array)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::38 => @param _values    An array containing amounts of each token being transferred (order and length must match _ids array)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol::12 => * @param _data       Arbitrary length data signed on the behalf of address(this)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol::12 => * @param _data Arbitrary length data signed on the behalf of address(this)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol::21 => ///                     data length as a uint256 (=> 32 bytes),
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol::30 => let length := mload(transactions)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol::34 => } lt(i, length) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol::48 => // We offset the load address by 85 byte (operation byte + 20 address bytes + 32 value bytes + 32 data length bytes)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol::61 => // Next entry starts at 85 byte + data length
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::14 => ///                     data length as a uint256 (=> 32 bytes),
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::24 => let length := mload(transactions)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::28 => } lt(i, length) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::42 => // We offset the load address by 85 byte (operation byte + 20 address bytes + 32 value bytes + 32 data length bytes)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::56 => // Next entry starts at 85 byte + data length
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Strings.sol::20 => uint256 length = Math.log10(value) + 1;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Strings.sol::21 => string memory buffer = new string(length);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Strings.sol::25 => ptr := add(buffer, add(32, length))
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Strings.sol::50 => * @dev Converts a `uint256` to its ASCII `string` hexadecimal representation with fixed length.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Strings.sol::52 => function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Strings.sol::53 => bytes memory buffer = new bytes(2 * length + 2);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Strings.sol::56 => for (uint256 i = 2 * length + 1; i > 1; --i) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Strings.sol::60 => require(value == 0, "Strings: hex length insufficient");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Strings.sol::65 => * @dev Converts an `address` with fixed length of 20 bytes to its not checksummed ASCII `string` hexadecimal representation.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol::37 => return PaymasterData(paymasterId, signature, signature.length);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::107 => require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
```
#### Tools used
Manual code review on vs code

### 3rd BUG
Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: [G003](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:
```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::236 => if (refundInfo.gasPrice > 0) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol::16 => * MUST NOT modify state (using STATICCALL for solc < 0.5, view modifier for solc > 0.5)
```
#### Tools used
Manual code review on vs code

### 4th BUG
Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: 
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
#### Findings:
```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/Proxy.sol::10 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1, and is validated in the constructor */
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::39 => // keccak256(
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::45 => // keccak256(
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::136 => return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::217 => txHash = keccak256(txHashData);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::347 => _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::416 => return keccak256(encodeTransactionData(_tx, refundInfo, _nonce));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::430 => keccak256(
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::435 => keccak256(_tx.data),
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::34 => bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::70 => bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::71 => bytes32 hash = keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, keccak256(code)));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::39 => // keccak256(
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::45 => // keccak256(
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::136 => return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::217 => txHash = keccak256(txHashData);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::342 => _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::406 => return keccak256(encodeTransactionData(_tx, refundInfo, _nonce));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::420 => keccak256(
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::425 => keccak256(_tx.data),
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::197 => return keccak256(abi.encode(userOp.hash(), address(this), block.chainid));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol::74 => return keccak256(pack(userOp));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol::11 => // keccak256("fallback_manager.handler.address")
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol::9 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1 */
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol::13 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::11 => This function MUST return `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))` (i.e. 0xf23a6e61) if it accepts the transfer.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::13 => Return of any other value than the prescribed keccak256 generated value MUST result in the transaction being reverted by the caller.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::19 => @return           `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))`
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::32 => This function MUST return `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))` (i.e. 0xbc197c81) if it accepts the transfer(s).
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::34 => Return of any other value than the prescribed keccak256 generated value MUST result in the transaction being reverted by the caller.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::40 => @return           `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))`
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol::16 => /// @return `bytes4(keccak256("onERC721Received(address,address,uint256,bytes)"))`
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol::10 => *   > The bytes4 magic value to return when signature is valid is 0x20c13b0b : bytes4(keccak256("isValidSignature(bytes,bytes)")
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol::26 => *   > The bytes4 magic value to return when signature is valid is 0x20c13b0b : bytes4(keccak256("isValidSignature(bytes,bytes)")
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol::28 => * @param _hash       keccak256 hash that was signed
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol::5 => // bytes4(keccak256("isValidSignature(bytes,bytes)")
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::80 => return keccak256(abi.encode(
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::83 => keccak256(userOp.initCode),
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::84 => keccak256(userOp.callData),
```
#### Tools used
Manual code review on vs code

### 5th BUG
Long Revert Strings

#### Impact
Issue Information: 
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol::8 => import "@account-abstraction/contracts/interfaces/IAccount.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol::9 => import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/Proxy.sol::10 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1, and is validated in the constructor */
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::7 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::13 => import "./common/SecuredTokenTransfer.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::14 => import "./interfaces/ISignatureValidator.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::16 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::40 => //     "EIP712Domain(uint256 chainId,address verifyingContract)"
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::46 => //     "AccountTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::18 => require(_baseImpl != address(0), "base wallet address can not be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::7 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::13 => import "./common/SecuredTokenTransfer.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::14 => import "./interfaces/ISignatureValidator.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::16 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::40 => //     "EIP712Domain(uint256 chainId,address verifyingContract)"
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::46 => //     "WalletTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol::7 => import "@openzeppelin/contracts/access/Ownable.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::15 => import "../interfaces/IAggregatedAccount.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol::9 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1 */
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol::13 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::4 => import "../interfaces/ERC1155TokenReceiver.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::5 => import "../interfaces/ERC721TokenReceiver.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::6 => import "../interfaces/ERC777TokensRecipient.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::11 => This function MUST return `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))` (i.e. 0xf23a6e61) if it accepts the transfer.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::19 => @return           `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))`
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::32 => This function MUST return `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))` (i.e. 0xbc197c81) if it accepts the transfer(s).
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::40 => @return           `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))`
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol::16 => /// @return `bytes4(keccak256("onERC721Received(address,address,uint256,bytes)"))`
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol::27 => require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol::7 => import "@openzeppelin/contracts/access/Ownable.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol::8 => import "@account-abstraction/contracts/interfaces/IPaymaster.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol::9 => import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol::4 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol::5 => import "@account-abstraction/contracts/interfaces/UserOperation.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::6 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::7 => import "@openzeppelin/contracts/proxy/utils/Initializable.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::8 => import "@openzeppelin/contracts/utils/Address.sol";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::36 => require(address(_entryPoint) != address(0), "VerifyingPaymaster: Entrypoint can not be zero address");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::37 => require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::42 => revert("Deposit must be for a paymasterId. Use depositFor");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::49 => require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::50 => require(paymasterId != address(0), "Paymaster Id can not be zero address");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::66 => require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::106 => // we only "require" it here so that the revert reason on invalid signature will be of "VerifyingPaymaster", and not "ECDSA"
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::107 => require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::108 => require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::109 => require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");
```
#### Tools used
Manual code review on vs code

### 6th BUG
Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: 
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::198 => // initial gas = 21k + non_zero_bytes * 16 + zero_bytes * 4
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::201 => //console.log("init %s", 21000 + msg.data.length * 8);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::198 => // initial gas = 21k + non_zero_bytes * 16 + zero_bytes * 4
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::201 => //console.log("init %s", 21000 + msg.data.length * 8);
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::35 => // (a + b) / 2 can overflow.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::36 => return (a & b) + (a ^ b) / 2;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::52 => * @dev Original credit to Remco Bloemen under MIT license (https://xn--2-umb.com/21/muldiv)
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::63 => // variables such that product = prod1 * 2^256 + prod0.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::170 => // → `2**(k/2) <= sqrt(a) < 2**((k+1)/2) <= 2**(k/2 + 1)`
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::172 => // Consequently, `2**(log2(a) / 2)` is a good first approximation of `sqrt(a)` with at least 1 correct bit.
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::273 => if (value >= 10**8) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::274 => value /= 10**8;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::277 => if (value >= 10**4) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::278 => value /= 10**4;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::281 => if (value >= 10**2) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol::282 => value /= 10**2;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::97 => function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 /*userOpHash*/, uint256 requiredPreFund)
```
#### Tools used
Manual code review on vs code