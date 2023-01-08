# c4udit Report

### Summery
[L001] Unspecific Compiler Version Pragma
[N001] Line length must be no more than 120
[N002] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’
[L002] REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()
[L003] EVENT IS MISSING INDEXED FIELDS
[N003] REQUIRE() / REVERT() STATEMENTS SHOULD HAVE DESCRIPTIVE REASON STRINGS
[N004] WRONG INDENTATION AND CODE STYLE
[N005] INCORRECT FUNCTION ORDER 
[L004] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT
[N006] INCONSISTANT FUNCTION NAMING
[L005] USE OF BLOCK.TIMESTAMP
[N007] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

## Issues found

### [L001] Unspecific Compiler Version Pragma

#### Impact
Issue Information:(https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::6 => pragma solidity ^0.8.12;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol::2 => pragma solidity ^0.8.12;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [N001] Line length must be no more than 120

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::39 
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::45 
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::57 
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::60
scw-contracts\contracts\smart-contract-wallet\Proxy.sol::10
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::42
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::46
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::186
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::222
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::223
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::227
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::228
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::229
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::230
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::231
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::233
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::239
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::300
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::320
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::327
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::339
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::342
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::346
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::356
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::257
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::489
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol::24
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol::33
```
### Mitigation
Consider breaking the lines for more readabality

#### Tools used
Solhint

### [N002] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::12 => import "../interfaces/IAccount.sol";
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::13 => import "../interfaces/IPaymaster.sol";
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::15 => import "../interfaces/IAggregatedAccount.sol";
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::16 => import "../interfaces/IEntryPoint.sol";
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::17 => import "../utils/Exec.sol";
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::18 => import "./StakeManager.sol";
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::19 => import "./SenderCreator.sol";
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol::4 => import "../interfaces/IStakeManager.sol";
scw-contracts\contracts\smart-contract-wallet\base\Executor.sol::4 => import "../common/Enum.sol";
scw-contracts\contracts\smart-contract-wallet\base\FallbackManager.sol::4 => import "../common/SelfAuthorized.sol";
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::4 => import "../common/Enum.sol";
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::5 => import "../common/SelfAuthorized.sol";
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::6 => import "./Executor.sol";
scw-contracts\contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol::4 => import "../interfaces/ERC1155TokenReceiver.sol";
scw-contracts\contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol::5 => import "../interfaces/ERC721TokenReceiver.sol";
scw-contracts\contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol::6 => import "../interfaces/ERC777TokensRecipient.sol";
scw-contracts\contracts\smart-contract-wallet\handler\DefaultCallbackHandler.sol::7 => import "../interfaces/IERC165.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol::4 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol::5 => import "@account-abstraction/contracts/interfaces/UserOperation.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::5 => import "../../BasePaymaster.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::6 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::7 => import "@openzeppelin/contracts/proxy/utils/Initializable.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::8 => import "@openzeppelin/contracts/utils/Address.sol";
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::9 => import "../../PaymasterHelpers.sol";
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::8 => import "@account-abstraction/contracts/interfaces/IAccount.sol";
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::9 => import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::10 => import "./common/Enum.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::4 => import "./libs/LibAddress.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::7 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::8 => import "./BaseSmartAccount.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::9 => import "./common/Singleton.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::10 => import "./base/ModuleManager.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::11 => import "./base/FallbackManager.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::11 => import "./base/FallbackManager.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::12 => import "./common/SignatureDecoder.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::13 => import "./common/SecuredTokenTransfer.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::14 => import "./interfaces/ISignatureValidator.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::15 => import "./interfaces/IERC165.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::16 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol::4 => import "./Proxy.sol";
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol::5 => import "./BaseSmartAccount.sol"; 
```

#### Tools used
Manual

### [L002] REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```

#### Tools used
Manual

### [L003] EVENT IS MISSING INDEXED FIELDS

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::64 => event ImplementationUpdated(address _scw, string version, address newImplementation);
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::65 => event EntryPointChanged(address oldEntryPoint, address newEntryPoint);
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::67 => event WalletHandlePayment(bytes32 txHash, uint256 payment);
scw-contracts\contracts\smart-contract-wallet\base\Executor.sol::9 => event ExecutionFailure(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
scw-contracts\contracts\smart-contract-wallet\base\Executor.sol::10 => event ExecutionSuccess(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
scw-contracts\contracts\smart-contract-wallet\base\FallbackManager.sol::9 => event ChangedFallbackHandler(address handler);
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::11 => event EnabledModule(address module);
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::12 => event DisabledModule(address module);
```
#### Tools used
Manual

### [N003] REQUIRE() / REVERT() STATEMENTS SHOULD HAVE DESCRIPTIVE REASON STRINGS

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::224 => require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::232 => require(success || _tx.targetTxGas != 0 || refundInfo.gasPrice != 0, "BSA013");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::262 => require(success, "BSA011");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::265 => require(transferToken(gasToken, receiver, payment), "BSA012");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::286 => require(success, "BSA011");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::289 => require(transferToken(gasToken, receiver, payment), "BSA012");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::294 => revert(string(abi.encodePacked(requiredGas)));
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::322 => require(uint256(s) >= uint256(1) * 65, "BSA021");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::325 => require(uint256(s) + 32 <= signatures.length, "BSA022");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::333 => require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::342 => require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::371 => require(execute(to, value, data, operation, gasleft()));
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::528 => require(req);
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::21 => require(modules[SENTINEL_MODULES] == address(0), "BSA100");
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::25 => require(execute(to, 0, data, Enum.Operation.DelegateCall, gasleft()), "BSA000");
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::34 => require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::36 => require(modules[module] == address(0), "BSA102");
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::49 => require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::50 => require(modules[prevModule] == module, "BSA103");
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol::68 => require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```

#### Tools used
Manual

### [N004] WRONG INDENTATION AND CODE STYLE

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::73 => unchecked {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::185 => unchecked {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::249 => unchecked {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::290 => internal returns (uint256 gasUsedByValidateAccountPrepayment, address actualAggregator, uint256 deadline) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::291 => unchecked {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::350 => unchecked {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::386 => private returns (address actualAggregator, uint256 deadline) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::417 => unchecked {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::442 => unchecked {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::485 => unchecked {

scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::119 =>function _postOp(
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::123 => ) internal virtual override {
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol::129 => }
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::13-17
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::21-25
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::61 => external override virtual returns (uint256 deadline) {
scw-contracts\contracts\smart-contract-wallet\BaseSmartAccount.sol::88 => internal virtual returns (uint256 deadline);
scw-contracts\contracts\smart-contract-wallet\Proxy.sol::16-19
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::19-28
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::156 => public view
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::507 => internal override virtual returns (uint256 deadline) {
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol::69-71
```

#### Mitigation
Consider using prettier for better readabality

#### Tools used
Manual

### [N005] INCORRECT FUNCTION ORDER 

#### Findings
Following files:
```
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol
scw-contracts\contracts\smart-contract-wallet\base\FallbackManager.sol
scw-contracts\contracts\smart-contract-wallet\base\ModuleManager.sol
scw-contracts\contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol
scw-contracts\contracts\smart-contract-wallet\SmartAccountFactory.sol
```

#### Mitigation
Function order in solidity:
```
constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
```
#### Tools used
Manual

### [L004] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

#### Findings :
```
scw-contracts\contracts\smart-contract-wallet\Proxy.sol::11 => bytes32 internal constant _IMPLEMENTATION_SLOT 
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::42 => bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH 
scw-contracts\contracts\smart-contract-wallet\SmartAccount.sol::48 => bytes32 internal constant ACCOUNT_TX_TYPEHASH 
```

#### Tools used
Manual

### [N006] INCONSISTANT FUNCTION NAMING 

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::484 => function getUserOpGasPrice(MemoryUserOp memory mUserOp) internal view returns (uint256) {
and etc ...
```

#### Mitigation
Some Internal function names start with "_" and some do not. Consider consistant naming to avoid confusion.

#### Tools used
Manual

### [L005] USE OF BLOCK.TIMESTAMP

#### Findings:
```
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::321 => if (_deadline != 0 && _deadline < block.timestamp) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol::365 => if (_deadline != 0 && _deadline < block.timestamp) {
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol::84 => uint64 withdrawTime = uint64(block.timestamp) + info.unstakeDelaySec;
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol::101 => require(info.withdrawTime <= block.timestamp, "Stake withdrawal is not due");
```

#### Tools used
Manual

### [N007] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

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