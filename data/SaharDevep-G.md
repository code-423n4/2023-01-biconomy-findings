# c4udit Report


### Summery
[G001] Don't Initialize Variables with Default Value
[G002] Cache Array Length Outside of Loop
[G003] Use != 0 instead of > 0 for Unsigned Integer Comparison
[G004] Use immutable for OpenZeppelin AccessControl's Roles Declarations
[G005] Long Revert Strings
[G006] Use Shift Right/Left instead of Division/Multiplication if possible
[G007] TIGHT VARIABLE PACKING
[G008] MULTIPLE ACCESSES OF A MAPPING/ARRAY SHOULD USE A LOCAL VARIABLE CACHE
[G009] STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE
[G010] USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
[G011] USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS
[G012] ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
[G013] INLINE THE MODIFIER THAT IS USED ONCE
[G014] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

## Issues found

### [G001] Don't Initialize Variables with Default Value

#### Impact
Issue Information: (https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::234 => uint256 payment = 0;
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::310 => uint256 i = 0;
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::468 => for (uint i = 0; i < dest.length;) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::74 => for (uint256 i = 0; i < opslen; i++) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::78 => uint256 collected = 0;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::80 => for (uint256 i = 0; i < opslen; i++) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::99 => uint256 totalOps = 0;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::100 => for (uint256 i = 0; i < opasLen; i++) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::106 => uint256 opIndex = 0;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::107 => for (uint256 a = 0; a < opasLen; a++) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::112 => for (uint256 i = 0; i < opslen; i++) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::126 => uint256 collected = 0;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::128 => for (uint256 a = 0; a < opasLen; a++) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::134 => for (uint256 i = 0; i < opslen; i++) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::313 => uint256 missingAccountFunds = 0;
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::119 => uint256 moduleCount = 0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G002] Cache Array Length Outside of Loop

#### Impact
Issue Information:(https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::64 => if (userOp.initCode.length == 0) {
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::201 => //console.log("init %s", 21000 + msg.data.length * 8);
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::324 => // Check that signature data pointer (s) is in bounds (points to the length of data -> 32 bytes)
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::325 => require(uint256(s) + 32 <= signatures.length, "BSA022");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::327 => // Check if the contract signature is in bounds: start of data is s + 32 and end is start + signature length
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::333 => require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::467 => require(dest.length == func.length, "wrong array lengths");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::468 => for (uint i = 0; i < dest.length;) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::70 => uint256 opslen = ops.length;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::98 => uint256 opasLen = opsPerAggregator.length;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::101 => totalOps += opsPerAggregator[i].userOps.length;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::111 => uint256 opslen = ops.length;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::132 => uint256 opslen = ops.length;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::174 => if (callData.length > 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::178 => if (result.length > 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::212 => if (paymasterAndData.length > 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::213 => require(paymasterAndData.length >= 20, "AA93 invalid paymasterAndData");
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::237 => address factory = initCode.length >= 20 ? address(bytes20(initCode[0 : 20])) : address(0);
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::262 => if (initCode.length != 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::264 => if (sender.code.length != 0) revert FailedOp(opIndex, address(0), "AA10 sender already constructed");
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::268 => if (sender1.code.length == 0) revert FailedOp(opIndex, address(0), "AA15 initCode must create sender");
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::299 => if (sender.code.length == 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::303 => if (mUserOp.paymaster != address(0) && mUserOp.paymaster.code.length == 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::452 => if (context.length > 0) {
scw-contracts\contracts\smart-contract-wallet\libs\MultiSend.sol::21 => ///                     data length as a uint256 (=> 32 bytes),
scw-contracts\contracts\smart-contract-wallet\libs\MultiSend.sol::30 => let length := mload(transactions)
scw-contracts\contracts\smart-contract-wallet\libs\MultiSend.sol::34 => } lt(i, length) {
scw-contracts\contracts\smart-contract-wallet\libs\MultiSend.sol::48 => // We offset the load address by 85 byte (operation byte + 20 address bytes + 32 value bytes + 32 data length bytes)
scw-contracts\contracts\smart-contract-wallet\libs\MultiSend.sol::61 => // Next entry starts at 85 byte + data length
scw-contracts\contracts\smart-contract-wallet\libs\MultiSendCallOnly.sol::14 => ///                     data length as a uint256 (=> 32 bytes),
scw-contracts\contracts\smart-contract-wallet\libs\MultiSendCallOnly.sol::24 => let length := mload(transactions)
scw-contracts\contracts\smart-contract-wallet\libs\MultiSendCallOnly.sol::28 => } lt(i, length) {
scw-contracts\contracts\smart-contract-wallet\libs\MultiSendCallOnly.sol::42 => // We offset the load address by 85 byte (operation byte + 20 address bytes + 32 value bytes + 32 data length bytes)
scw-contracts\contracts\smart-contract-wallet\libs\MultiSendCallOnly.sol::56 => // Next entry starts at 85 byte + data length
scw-contracts\contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol::37 => return PaymasterData(paymasterId, signature, signature.length);
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::107 => require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G003] Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information:(https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::236 => if (refundInfo.gasPrice > 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::174 => if (callData.length > 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::178 => if (result.length > 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::212 => if (paymasterAndData.length > 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::452 => if (context.length > 0) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol::61 => require(_unstakeDelaySec > 0, "must specify unstake delay");
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol::64 => require(stake > 0, "no stake specified");
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol::99 => require(stake > 0, "No stake to withdraw");
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol::100 => require(info.withdrawTime > 0, "must call unlockStake() first");
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G004] Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information:(https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\Proxy.sol::10 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1, and is validated in the constructor */
scw-contracts\contracts\smart-contract-wallet\Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::39 => // keccak256(
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::45 => // keccak256(
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::136 => return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::217 => txHash = keccak256(txHashData);
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::347 => _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::416 => return keccak256(encodeTransactionData(_tx, refundInfo, _nonce));
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::430 => keccak256(
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::435 => keccak256(_tx.data),
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol::34 => bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol::70 => bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol::71 => bytes32 hash = keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, keccak256(code)));
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::197 => return keccak256(abi.encode(userOp.hash(), address(this), block.chainid));
scw-contracts\contracts\smart-contract-wallet\base\FallbackManager.sol::11 => // keccak256("fallback_manager.handler.address")
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::80 => return keccak256(abi.encode(
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::83 => keccak256(userOp.initCode),
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::84 => keccak256(userOp.callData),
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G005] Long Revert Strings

#### Impact
Issue Information:(https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::8 => import "@account-abstraction/contracts/interfaces/IAccount.sol";
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::9 => import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";
scw-contracts\contracts\smart-contract-wallet\Proxy.sol::10 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1, and is validated in the constructor */
scw-contracts\contracts\smart-contract-wallet\Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::7 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::13 => import "./common/SecuredTokenTransfer.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::14 => import "./interfaces/ISignatureValidator.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::16 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::40 => //     "EIP712Domain(uint256 chainId,address verifyingContract)"
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::46 => //     "AccountTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol::18 => require(_baseImpl != address(0), "base wallet address can not be zero");
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::15 => import "../interfaces/IAggregatedAccount.sol";
scw-contracts\contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol::4 => import "../interfaces/ERC1155TokenReceiver.sol";
scw-contracts\contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol::5 => import "../interfaces/ERC721TokenReceiver.sol";
scw-contracts\contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol::6 => import "../interfaces/ERC777TokensRecipient.sol";
scw-contracts\contracts\smart-contract-wallet\libs\MultiSend.sol::27 => require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");
scw-contracts\contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol::4 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol::5 => import "@account-abstraction/contracts/interfaces/UserOperation.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::6 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::7 => import "@openzeppelin/contracts/proxy/utils/Initializable.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::8 => import "@openzeppelin/contracts/utils/Address.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::36 => require(address(_entryPoint) != address(0), "VerifyingPaymaster: Entrypoint can not be zero address");
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::37 => require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::42 => revert("Deposit must be for a paymasterId. Use depositFor");
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::49 => require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::50 => require(paymasterId != address(0), "Paymaster Id can not be zero address");
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::66 => require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::106 => // we only "require" it here so that the revert reason on invalid signature will be of "VerifyingPaymaster", and not "ECDSA"
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::107 => require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::108 => require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::109 => require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G006] Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information:(https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::198 => // initial gas = 21k + non_zero_bytes * 16 + zero_bytes * 4
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::201 => //console.log("init %s", 21000 + msg.data.length * 8);
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::97 => function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 /*userOpHash*/, uint256 requiredPreFund)
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)


### [G007] TIGHT VARIABLE PACKING

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::12 => struct Transaction {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::145 => struct MemoryUserOp {
```

#### Mitigation 
Pack struct variables like this:
```
struct Transaction {
    address to;
    uint256 targetTxGas;
    uint256 value;
    bytes data;
    Enum.Operation operation;
    }

```

#### Tools used
Manual

### [G008] MULTIPLE ACCESSES OF A MAPPING/ARRAY SHOULD USE A LOCAL VARIABLE CACHE

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::20 => function setupModules(address to, bytes memory data) internal {
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::32 => function enableModule(address module) public authorized {
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::47 => function disableModule(address prevModule, address module) public authorized {
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::55 => function withdrawTo(address payable withdrawAddress, uint256 amount) public override {
```
#### Tools used
Manual

### [G009] STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE

#### Findings: 
```
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::33 => address public verifyingSigner;
```

#### Tools used
Manual

### [G010] USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::36 => string public constant VERSION = "1.0.2";
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol::11 => string public constant VERSION = "1.0.2";
scw-contracts\contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol::!2 => string public constant NAME = "Default Callback Handler";
scw-contracts\contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol::13 => string public constant VERSION = "1.0.0";
```

#### Tools used
Manual

### [G011] USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS

#### Findings:
Following files:
```
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol
scw-contracts\contracts\smart-contract-wallet\libs\MultiSend.sol
scw-contracts\contracts\smart-contract-wallet\libs\MultiSendCallOnly.sol
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol
```

#### Tools used
Manual

### [G012] ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::136 => return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::143 => abi.encode(
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::197 => return keccak256(abi.encode(userOp.hash(), address(this), block.chainid));
scw-contracts\contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol::28 => return abi.encode(data.paymasterId);
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::80 => return keccak256(abi.encode(
```

#### Tools used
Manual

### [G013] INLINE THE MODIFIER THAT IS USED ONCE

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::88 => modifier onlyEntryPoint {
```

#### Tools used
Manual

### [G014] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::507 => internal override virtual returns (uint256 deadline) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::168 => function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol::18 => function getDepositInfo(address account) public view returns (DepositInfo memory info) {
scw-contracts\contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol::27 => ) internal pure returns (bytes memory context) {
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::98 => external view override returns (bytes memory context, uint256 deadline) {
```

#### Tools used
Manual

