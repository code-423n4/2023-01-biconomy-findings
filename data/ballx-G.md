# c4udit Report

## Files analyzed
- contracts/smart-contract-wallet/BaseSmartAccount.sol
- contracts/smart-contract-wallet/Proxy.sol
- contracts/smart-contract-wallet/SmartAccount.sol
- contracts/smart-contract-wallet/SmartAccountFactory.sol
- contracts/smart-contract-wallet/SmartAccountNoAuth.sol
- contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol
- contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol
- contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol
- contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol
- contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol
- contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol
- contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol
- contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol
- contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol
- contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol
- contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol
- contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol
- contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol
- contracts/smart-contract-wallet/aa-4337/samples/IOracle.sol
- contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol
- contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol
- contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountForTokens.sol
- contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccount.sol
- contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccountFactory.sol
- contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol
- contracts/smart-contract-wallet/aa-4337/samples/TokenPaymaster.sol
- contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol
- contracts/smart-contract-wallet/aa-4337/test/TestCounter.sol
- contracts/smart-contract-wallet/aa-4337/test/TestExpirePaymaster.sol
- contracts/smart-contract-wallet/aa-4337/test/TestExpiryAccount.sol
- contracts/smart-contract-wallet/aa-4337/test/TestOracle.sol
- contracts/smart-contract-wallet/aa-4337/test/TestPaymasterAcceptAll.sol
- contracts/smart-contract-wallet/aa-4337/test/TestToken.sol
- contracts/smart-contract-wallet/aa-4337/test/TestUtil.sol
- contracts/smart-contract-wallet/aa-4337/utils/Exec.sol
- contracts/smart-contract-wallet/base/Executor.sol
- contracts/smart-contract-wallet/base/FallbackManager.sol
- contracts/smart-contract-wallet/base/ModuleManager.sol
- contracts/smart-contract-wallet/common/Enum.sol
- contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol
- contracts/smart-contract-wallet/common/SelfAuthorized.sol
- contracts/smart-contract-wallet/common/SignatureDecoder.sol
- contracts/smart-contract-wallet/common/Singleton.sol
- contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol
- contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol
- contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol
- contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol
- contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol
- contracts/smart-contract-wallet/interfaces/IERC165.sol
- contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol
- contracts/smart-contract-wallet/libs/LibAddress.sol
- contracts/smart-contract-wallet/libs/Math.sol
- contracts/smart-contract-wallet/libs/MultiSend.sol
- contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol
- contracts/smart-contract-wallet/libs/Strings.sol
- contracts/smart-contract-wallet/paymasters/BasePaymaster.sol
- contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol
- contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
- contracts/smart-contract-wallet/test/Button.sol
- contracts/smart-contract-wallet/test/MockToken.sol
- contracts/smart-contract-wallet/test/StakedTestToken.sol
- contracts/smart-contract-wallet/test/StorageSetter.sol
- contracts/smart-contract-wallet/utils/Decoder.sol
- contracts/smart-contract-wallet/utils/GasEstimator.sol
- contracts/smart-contract-wallet/utils/GasEstimatorSmartAccount.sol

## Issues found

### Don't Initialize Variables with Default Value

#### Impact
Issue Information: [G001](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:
```
contracts/smart-contract-wallet/SmartAccount.sol::234 => uint256 payment = 0;
contracts/smart-contract-wallet/SmartAccount.sol::310 => uint256 i = 0;
contracts/smart-contract-wallet/SmartAccount.sol::468 => for (uint i = 0; i < dest.length;) {
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::234 => uint256 payment = 0;
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::305 => uint256 i = 0;
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::458 => for (uint i = 0; i < dest.length;) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::74 => for (uint256 i = 0; i < opslen; i++) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::78 => uint256 collected = 0;
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::80 => for (uint256 i = 0; i < opslen; i++) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::99 => uint256 totalOps = 0;
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::100 => for (uint256 i = 0; i < opasLen; i++) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::106 => uint256 opIndex = 0;
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::107 => for (uint256 a = 0; a < opasLen; a++) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::112 => for (uint256 i = 0; i < opslen; i++) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::126 => uint256 collected = 0;
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::128 => for (uint256 a = 0; a < opasLen; a++) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::134 => for (uint256 i = 0; i < opslen; i++) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::313 => uint256 missingAccountFunds = 0;
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol::71 => for (uint256 i = 0; i < dest.length; i++) {
contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol::18 => uint sum = 0;
contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol::19 => for (uint i = 0; i < userOps.length; i++) {
contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol::38 => uint sum = 0;
contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol::39 => for (uint i = 0; i < userOps.length; i++) {
contracts/smart-contract-wallet/base/ModuleManager.sol::119 => uint256 moduleCount = 0;
contracts/smart-contract-wallet/libs/Math.sol::206 => uint256 result = 0;
contracts/smart-contract-wallet/libs/Math.sol::259 => uint256 result = 0;
contracts/smart-contract-wallet/libs/Math.sol::310 => uint256 result = 0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Cache Array Length Outside of Loop

#### Impact
Issue Information: [G002](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
contracts/smart-contract-wallet/BaseSmartAccount.sol::64 => if (userOp.initCode.length == 0) {
contracts/smart-contract-wallet/SmartAccount.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
contracts/smart-contract-wallet/SmartAccount.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
contracts/smart-contract-wallet/SmartAccount.sol::201 => //console.log("init %s", 21000 + msg.data.length * 8);
contracts/smart-contract-wallet/SmartAccount.sol::324 => // Check that signature data pointer (s) is in bounds (points to the length of data -> 32 bytes)
contracts/smart-contract-wallet/SmartAccount.sol::325 => require(uint256(s) + 32 <= signatures.length, "BSA022");
contracts/smart-contract-wallet/SmartAccount.sol::327 => // Check if the contract signature is in bounds: start of data is s + 32 and end is start + signature length
contracts/smart-contract-wallet/SmartAccount.sol::333 => require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");
contracts/smart-contract-wallet/SmartAccount.sol::467 => require(dest.length == func.length, "wrong array lengths");
contracts/smart-contract-wallet/SmartAccount.sol::468 => for (uint i = 0; i < dest.length;) {
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::201 => //console.log("init %s", 21000 + msg.data.length * 8);
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::319 => // Check that signature data pointer (s) is in bounds (points to the length of data -> 32 bytes)
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::320 => require(uint256(s) + 32 <= signatures.length, "BSA022");
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::322 => // Check if the contract signature is in bounds: start of data is s + 32 and end is start + signature length
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::328 => require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::457 => require(dest.length == func.length, "wrong array lengths");
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::458 => for (uint i = 0; i < dest.length;) {
contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol::39 => if (userOp.initCode.length == 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::70 => uint256 opslen = ops.length;
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::98 => uint256 opasLen = opsPerAggregator.length;
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::101 => totalOps += opsPerAggregator[i].userOps.length;
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::111 => uint256 opslen = ops.length;
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::132 => uint256 opslen = ops.length;
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::174 => if (callData.length > 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::178 => if (result.length > 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::212 => if (paymasterAndData.length > 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::213 => require(paymasterAndData.length >= 20, "AA93 invalid paymasterAndData");
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::237 => address factory = initCode.length >= 20 ? address(bytes20(initCode[0 : 20])) : address(0);
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::262 => if (initCode.length != 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::264 => if (sender.code.length != 0) revert FailedOp(opIndex, address(0), "AA10 sender already constructed");
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::268 => if (sender1.code.length == 0) revert FailedOp(opIndex, address(0), "AA15 initCode must create sender");
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::299 => if (sender.code.length == 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::303 => if (mUserOp.paymaster != address(0) && mUserOp.paymaster.code.length == 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::452 => if (context.length > 0) {
contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol::40 => * An event emitted if the UserOperation "callData" reverted with non-zero length
contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol::22 => *  zero length to signify postOp is not required.
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::133 => require(paymasterAndData.length == 20+20, "DepositPaymaster: paymasterAndData must specify token");
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol::70 => require(dest.length == func.length, "wrong array lengths");
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol::71 => for (uint256 i = 0; i < dest.length; i++) {
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol::30 => uint codeSize = addr.code.length;
contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccountFactory.sol::29 => uint codeSize = addr.code.length;
contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol::19 => for (uint i = 0; i < userOps.length; i++) {
contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol::24 => require(signature.length == 32, "TestSignatureValidator: sig must be uint");
contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol::39 => for (uint i = 0; i < userOps.length; i++) {
contracts/smart-contract-wallet/aa-4337/samples/TokenPaymaster.sol::78 => if (userOp.initCode.length != 0) {
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::62 => uint256 sigLength = paymasterAndData.length - 20;
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::65 => require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::37 => @param _ids       An array containing ids of each token being transferred (order and length must match _values array)
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::38 => @param _values    An array containing amounts of each token being transferred (order and length must match _ids array)
contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol::12 => * @param _data       Arbitrary length data signed on the behalf of address(this)
contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol::12 => * @param _data Arbitrary length data signed on the behalf of address(this)
contracts/smart-contract-wallet/libs/MultiSend.sol::21 => ///                     data length as a uint256 (=> 32 bytes),
contracts/smart-contract-wallet/libs/MultiSend.sol::30 => let length := mload(transactions)
contracts/smart-contract-wallet/libs/MultiSend.sol::34 => } lt(i, length) {
contracts/smart-contract-wallet/libs/MultiSend.sol::48 => // We offset the load address by 85 byte (operation byte + 20 address bytes + 32 value bytes + 32 data length bytes)
contracts/smart-contract-wallet/libs/MultiSend.sol::61 => // Next entry starts at 85 byte + data length
contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::14 => ///                     data length as a uint256 (=> 32 bytes),
contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::24 => let length := mload(transactions)
contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::28 => } lt(i, length) {
contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::42 => // We offset the load address by 85 byte (operation byte + 20 address bytes + 32 value bytes + 32 data length bytes)
contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::56 => // Next entry starts at 85 byte + data length
contracts/smart-contract-wallet/libs/Strings.sol::20 => uint256 length = Math.log10(value) + 1;
contracts/smart-contract-wallet/libs/Strings.sol::21 => string memory buffer = new string(length);
contracts/smart-contract-wallet/libs/Strings.sol::25 => ptr := add(buffer, add(32, length))
contracts/smart-contract-wallet/libs/Strings.sol::50 => * @dev Converts a `uint256` to its ASCII `string` hexadecimal representation with fixed length.
contracts/smart-contract-wallet/libs/Strings.sol::52 => function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
contracts/smart-contract-wallet/libs/Strings.sol::53 => bytes memory buffer = new bytes(2 * length + 2);
contracts/smart-contract-wallet/libs/Strings.sol::56 => for (uint256 i = 2 * length + 1; i > 1; --i) {
contracts/smart-contract-wallet/libs/Strings.sol::60 => require(value == 0, "Strings: hex length insufficient");
contracts/smart-contract-wallet/libs/Strings.sol::65 => * @dev Converts an `address` with fixed length of 20 bytes to its not checksummed ASCII `string` hexadecimal representation.
contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol::37 => return PaymasterData(paymasterId, signature, signature.length);
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::107 => require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: [G003](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:
```
contracts/smart-contract-wallet/SmartAccount.sol::236 => if (refundInfo.gasPrice > 0) {
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::236 => if (refundInfo.gasPrice > 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::174 => if (callData.length > 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::178 => if (result.length > 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::212 => if (paymasterAndData.length > 0) {
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::452 => if (context.length > 0) {
contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol::61 => require(_unstakeDelaySec > 0, "must specify unstake delay");
contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol::64 => require(stake > 0, "no stake specified");
contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol::99 => require(stake > 0, "No stake to withdraw");
contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol::100 => require(info.withdrawTime > 0, "must call unlockStake() first");
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol::31 => if (codeSize > 0) {
contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccountFactory.sol::30 => if (codeSize > 0) {
contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol::16 => * MUST NOT modify state (using STATICCALL for solc < 0.5, view modifier for solc > 0.5)
contracts/smart-contract-wallet/libs/Math.sol::147 => if (rounding == Rounding.Up && mulmod(x, y, denominator) > 0) {
contracts/smart-contract-wallet/libs/Math.sol::208 => if (value >> 128 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::212 => if (value >> 64 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::216 => if (value >> 32 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::220 => if (value >> 16 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::224 => if (value >> 8 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::228 => if (value >> 4 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::232 => if (value >> 2 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::236 => if (value >> 1 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::312 => if (value >> 128 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::316 => if (value >> 64 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::320 => if (value >> 32 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::324 => if (value >> 16 > 0) {
contracts/smart-contract-wallet/libs/Math.sol::328 => if (value >> 8 > 0) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: [G006](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
contracts/smart-contract-wallet/Proxy.sol::10 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1, and is validated in the constructor */
contracts/smart-contract-wallet/Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
contracts/smart-contract-wallet/SmartAccount.sol::39 => // keccak256(
contracts/smart-contract-wallet/SmartAccount.sol::45 => // keccak256(
contracts/smart-contract-wallet/SmartAccount.sol::136 => return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
contracts/smart-contract-wallet/SmartAccount.sol::217 => txHash = keccak256(txHashData);
contracts/smart-contract-wallet/SmartAccount.sol::347 => _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);
contracts/smart-contract-wallet/SmartAccount.sol::416 => return keccak256(encodeTransactionData(_tx, refundInfo, _nonce));
contracts/smart-contract-wallet/SmartAccount.sol::430 => keccak256(
contracts/smart-contract-wallet/SmartAccount.sol::435 => keccak256(_tx.data),
contracts/smart-contract-wallet/SmartAccountFactory.sol::34 => bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
contracts/smart-contract-wallet/SmartAccountFactory.sol::70 => bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
contracts/smart-contract-wallet/SmartAccountFactory.sol::71 => bytes32 hash = keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, keccak256(code)));
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::39 => // keccak256(
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::45 => // keccak256(
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::136 => return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::217 => txHash = keccak256(txHashData);
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::342 => _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::406 => return keccak256(encodeTransactionData(_tx, refundInfo, _nonce));
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::420 => keccak256(
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::425 => keccak256(_tx.data),
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::197 => return keccak256(abi.encode(userOp.hash(), address(this), block.chainid));
contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol::74 => return keccak256(pack(userOp));
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol::44 => return Create2.computeAddress(bytes32(salt), keccak256(abi.encodePacked(
contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccountFactory.sol::43 => return Create2.computeAddress(bytes32(salt), keccak256(abi.encodePacked(
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::39 => return keccak256(abi.encode(
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::42 => keccak256(userOp.initCode),
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::43 => keccak256(userOp.callData),
contracts/smart-contract-wallet/base/FallbackManager.sol::11 => // keccak256("fallback_manager.handler.address")
contracts/smart-contract-wallet/common/Singleton.sol::9 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1 */
contracts/smart-contract-wallet/common/Singleton.sol::13 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::11 => This function MUST return `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))` (i.e. 0xf23a6e61) if it accepts the transfer.
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::13 => Return of any other value than the prescribed keccak256 generated value MUST result in the transaction being reverted by the caller.
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::19 => @return           `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))`
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::32 => This function MUST return `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))` (i.e. 0xbc197c81) if it accepts the transfer(s).
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::34 => Return of any other value than the prescribed keccak256 generated value MUST result in the transaction being reverted by the caller.
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::40 => @return           `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))`
contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol::16 => /// @return `bytes4(keccak256("onERC721Received(address,address,uint256,bytes)"))`
contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol::10 => *   > The bytes4 magic value to return when signature is valid is 0x20c13b0b : bytes4(keccak256("isValidSignature(bytes,bytes)")
contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol::26 => *   > The bytes4 magic value to return when signature is valid is 0x20c13b0b : bytes4(keccak256("isValidSignature(bytes,bytes)")
contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol::28 => * @param _hash       keccak256 hash that was signed
contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol::5 => // bytes4(keccak256("isValidSignature(bytes,bytes)")
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::80 => return keccak256(abi.encode(
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::83 => keccak256(userOp.initCode),
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::84 => keccak256(userOp.callData),
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
contracts/smart-contract-wallet/BaseSmartAccount.sol::8 => import "@account-abstraction/contracts/interfaces/IAccount.sol";
contracts/smart-contract-wallet/BaseSmartAccount.sol::9 => import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";
contracts/smart-contract-wallet/Proxy.sol::10 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1, and is validated in the constructor */
contracts/smart-contract-wallet/Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
contracts/smart-contract-wallet/SmartAccount.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/smart-contract-wallet/SmartAccount.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/smart-contract-wallet/SmartAccount.sol::7 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
contracts/smart-contract-wallet/SmartAccount.sol::13 => import "./common/SecuredTokenTransfer.sol";
contracts/smart-contract-wallet/SmartAccount.sol::14 => import "./interfaces/ISignatureValidator.sol";
contracts/smart-contract-wallet/SmartAccount.sol::16 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
contracts/smart-contract-wallet/SmartAccount.sol::40 => //     "EIP712Domain(uint256 chainId,address verifyingContract)"
contracts/smart-contract-wallet/SmartAccount.sol::46 => //     "AccountTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"
contracts/smart-contract-wallet/SmartAccount.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
contracts/smart-contract-wallet/SmartAccount.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
contracts/smart-contract-wallet/SmartAccount.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
contracts/smart-contract-wallet/SmartAccountFactory.sol::18 => require(_baseImpl != address(0), "base wallet address can not be zero");
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::7 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::13 => import "./common/SecuredTokenTransfer.sol";
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::14 => import "./interfaces/ISignatureValidator.sol";
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::16 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::40 => //     "EIP712Domain(uint256 chainId,address verifyingContract)"
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::46 => //     "WalletTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol::7 => import "@openzeppelin/contracts/access/Ownable.sol";
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::15 => import "../interfaces/IAggregatedAccount.sol";
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::6 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::7 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::9 => import "@openzeppelin/contracts/access/Ownable.sol";
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::102 => require(unlockBlock[msg.sender] != 0 && block.number > unlockBlock[msg.sender], "DepositPaymaster: must unlockTokenDeposit");
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::115 => require(oracle != NULL_ORACLE, "DepositPaymaster: unsupported token");
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::130 => require(userOp.verificationGasLimit > COST_OF_POST, "DepositPaymaster: gas too low for postOp");
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::133 => require(paymasterAndData.length == 20+20, "DepositPaymaster: paymasterAndData must specify token");
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::138 => require(unlockBlock[account] == 0, "DepositPaymaster: deposit not locked");
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::139 => require(balances[token][account] >= maxTokenCost, "DepositPaymaster: deposit too low");
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol::8 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol::9 => import "@openzeppelin/contracts/proxy/utils/Initializable.sol";
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol::10 => import "@openzeppelin/contracts/proxy/utils/UUPSUpgradeable.sol";
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol::4 => import "@openzeppelin/contracts/utils/Create2.sol";
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol::5 => import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountForTokens.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccount.sol::4 => import "../interfaces/IAggregatedAccount.sol";
contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccountFactory.sol::4 => import "@openzeppelin/contracts/utils/Create2.sol";
contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccountFactory.sol::5 => import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";
contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol::24 => require(signature.length == 32, "TestSignatureValidator: sig must be uint");
contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol::26 => require(sig == sum, "TestSignatureValidator: aggregated signature mismatch (nonce sum)");
contracts/smart-contract-wallet/aa-4337/samples/TokenPaymaster.sol::6 => import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
contracts/smart-contract-wallet/aa-4337/samples/TokenPaymaster.sol::76 => require(userOp.verificationGasLimit > COST_OF_POST, "TokenPaymaster: gas too low for postOp");
contracts/smart-contract-wallet/aa-4337/samples/TokenPaymaster.sol::80 => require(balanceOf(userOp.sender) >= tokenPrefund, "TokenPaymaster: no balance (pre-create)");
contracts/smart-contract-wallet/aa-4337/samples/TokenPaymaster.sol::93 => require(factory == theFactory, "TokenPaymaster: wrong account factory");
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::7 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::64 => // we only "require" it here so that the revert reason on invalid signature will be of "VerifyingPaymaster", and not "ECDSA"
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::65 => require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::69 => require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterAndData[20 :]) || tx.origin == address(0), "VerifyingPaymaster: wrong signature");
contracts/smart-contract-wallet/aa-4337/test/TestCounter.sol::4 => //sample "receiver" contract, for testing "exec" from account.
contracts/smart-contract-wallet/aa-4337/test/TestToken.sol::4 => import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
contracts/smart-contract-wallet/common/Singleton.sol::9 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1 */
contracts/smart-contract-wallet/common/Singleton.sol::13 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::4 => import "../interfaces/ERC1155TokenReceiver.sol";
contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::5 => import "../interfaces/ERC721TokenReceiver.sol";
contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::6 => import "../interfaces/ERC777TokensRecipient.sol";
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::11 => This function MUST return `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))` (i.e. 0xf23a6e61) if it accepts the transfer.
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::19 => @return           `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))`
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::32 => This function MUST return `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))` (i.e. 0xbc197c81) if it accepts the transfer(s).
contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol::40 => @return           `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))`
contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol::16 => /// @return `bytes4(keccak256("onERC721Received(address,address,uint256,bytes)"))`
contracts/smart-contract-wallet/libs/MultiSend.sol::27 => require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");
contracts/smart-contract-wallet/paymasters/BasePaymaster.sol::7 => import "@openzeppelin/contracts/access/Ownable.sol";
contracts/smart-contract-wallet/paymasters/BasePaymaster.sol::8 => import "@account-abstraction/contracts/interfaces/IPaymaster.sol";
contracts/smart-contract-wallet/paymasters/BasePaymaster.sol::9 => import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";
contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol::4 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol::5 => import "@account-abstraction/contracts/interfaces/UserOperation.sol";
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::6 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::7 => import "@openzeppelin/contracts/proxy/utils/Initializable.sol";
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::8 => import "@openzeppelin/contracts/utils/Address.sol";
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::36 => require(address(_entryPoint) != address(0), "VerifyingPaymaster: Entrypoint can not be zero address");
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::37 => require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::42 => revert("Deposit must be for a paymasterId. Use depositFor");
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::49 => require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::50 => require(paymasterId != address(0), "Paymaster Id can not be zero address");
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::66 => require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::106 => // we only "require" it here so that the revert reason on invalid signature will be of "VerifyingPaymaster", and not "ECDSA"
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::107 => require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::108 => require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::109 => require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");
contracts/smart-contract-wallet/test/Button.sol::3 => import "@openzeppelin/contracts/access/Ownable.sol";
contracts/smart-contract-wallet/test/MockToken.sol::4 => import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
contracts/smart-contract-wallet/test/StakedTestToken.sol::4 => import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
contracts/smart-contract-wallet/test/StakedTestToken.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: [G008](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
contracts/smart-contract-wallet/SmartAccount.sol::198 => // initial gas = 21k + non_zero_bytes * 16 + zero_bytes * 4
contracts/smart-contract-wallet/SmartAccount.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
contracts/smart-contract-wallet/SmartAccount.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
contracts/smart-contract-wallet/SmartAccount.sol::201 => //console.log("init %s", 21000 + msg.data.length * 8);
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::198 => // initial gas = 21k + non_zero_bytes * 16 + zero_bytes * 4
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
contracts/smart-contract-wallet/SmartAccountNoAuth.sol::201 => //console.log("init %s", 21000 + msg.data.length * 8);
contracts/smart-contract-wallet/aa-4337/samples/TokenPaymaster.sol::70 => function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 /*userOpHash*/, uint256 requiredPreFund)
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::56 => function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 /*userOpHash*/, uint256 requiredPreFund)
contracts/smart-contract-wallet/aa-4337/test/TestOracle.sol::8 => return ethOutput * 2;
contracts/smart-contract-wallet/libs/Math.sol::35 => // (a + b) / 2 can overflow.
contracts/smart-contract-wallet/libs/Math.sol::36 => return (a & b) + (a ^ b) / 2;
contracts/smart-contract-wallet/libs/Math.sol::52 => * @dev Original credit to Remco Bloemen under MIT license (https://xn--2-umb.com/21/muldiv)
contracts/smart-contract-wallet/libs/Math.sol::63 => // variables such that product = prod1 * 2^256 + prod0.
contracts/smart-contract-wallet/libs/Math.sol::170 => // → `2**(k/2) <= sqrt(a) < 2**((k+1)/2) <= 2**(k/2 + 1)`
contracts/smart-contract-wallet/libs/Math.sol::172 => // Consequently, `2**(log2(a) / 2)` is a good first approximation of `sqrt(a)` with at least 1 correct bit.
contracts/smart-contract-wallet/libs/Math.sol::273 => if (value >= 10**8) {
contracts/smart-contract-wallet/libs/Math.sol::274 => value /= 10**8;
contracts/smart-contract-wallet/libs/Math.sol::277 => if (value >= 10**4) {
contracts/smart-contract-wallet/libs/Math.sol::278 => value /= 10**4;
contracts/smart-contract-wallet/libs/Math.sol::281 => if (value >= 10**2) {
contracts/smart-contract-wallet/libs/Math.sol::282 => value /= 10**2;
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol::97 => function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 /*userOpHash*/, uint256 requiredPreFund)
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountForTokens.sol::17 => token.approve(paymaster, type(uint256).max);
contracts/smart-contract-wallet/test/StakedTestToken.sol::21 => IERC20(STAKED_TOKEN).transferFrom(msg.sender, address(this), amount);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol::6 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol::6 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/samples/IOracle.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountForTokens.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccount.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccountFactory.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/samples/TokenPaymaster.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/test/TestCounter.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/test/TestExpirePaymaster.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/test/TestExpiryAccount.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/test/TestOracle.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/test/TestPaymasterAcceptAll.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/test/TestToken.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/test/TestUtil.sol::2 => pragma solidity ^0.8.12;
contracts/smart-contract-wallet/aa-4337/utils/Exec.sol::2 => pragma solidity >=0.7.5 <0.9.0;
contracts/smart-contract-wallet/paymasters/BasePaymaster.sol::2 => pragma solidity ^0.8.12;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

