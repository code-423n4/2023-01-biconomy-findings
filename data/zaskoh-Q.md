## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| L-01 | Unneccessary if condition for a check that is always true | 1 |
| L-02 | Function deployWallet can be removed as it is not possible to check for deployed addresses | 1 |
| L-03 | Always check important state vars before update | 2 |
| L-04 | Deviating interfaces to current EIP-4337 | 5 |
| L-05 | Return value not checked | 2 |
| L-06 | Inconsistent minimum stake delay | 1 |

Total: 12 instances over 6 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| NC-01 | Remove unnecessary _requireFromEntryPointOrOwner() check in function | 2 |
| NC-02 | Remove unused fields in events | 1 |
| NC-03 | Remove unused variable names from functions | 1 |
| NC-04 | Wrong handling of unused variables in function | 2 |
| NC-05 | Inconsistent solidity version | 5 |
| NC-06 | Update pragma versioning to a more recent version like 0.8.16 | 22 |
| NC-07 | Use specific imports instead of just a global import | 54 |
| NC-08 | Long lines | 7 |
| NC-09 | Add missing documentation | 39 |
| NC-10 | Typos | 3 |

Total: 136 instances over 10 issues

---

## Low Risk Issues

### L-01 Unneccessary if condition for a check that is always true
In SmartAccount is a if condition that is always true and can be removed as it is checked 3 lines above with a require.

```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol
166:     function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
167:         require(owner == address(0), "Already initialized");
168:         require(address(_entryPoint) == address(0), "Already initialized");
169:         require(_owner != address(0),"Invalid owner");
170:         require(_entryPointAddress != address(0), "Invalid Entrypoint");
171:         require(_handler != address(0), "Invalid Entrypoint");
172:         owner = _owner;
173:         _entryPoint =  IEntryPoint(payable(_entryPointAddress));
174:         if (_handler != address(0)) internalSetFallbackHandler(_handler); // @audit-info if can be removed and internalSetFallbackHandler(_handler); can always be set - require(_handler != address(0), "Invalid Entrypoint"); checks for this
175:         setupModules(address(0), bytes(""));
176:     }
```

### L-02 Function deployWallet can be removed as it is not possible to check for deployed addresses
SmartAccountFactory has the ability to deploy a new Wallet via deployWallet via a create instead of a create2. It also doesn't emit the SmartAccountCreated event and so any deployed Wallet via this function can't be found via the event and also not via the getAddressForCounterfactualWallet function. For Wallets created via this way, the frontends will not work.

```solidity
File: contracts/smart-contract-wallet/SmartAccountFactory.sol
53:     function deployWallet(address _owner, address _entryPoint, address _handler) public returns(address proxy){
54:         bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
55:         // solhint-disable-next-line no-inline-assembly
56:         assembly {
57:             proxy := create(0x0, add(0x20, deploymentData), mload(deploymentData))
58:         }
59:         BaseSmartAccount(proxy).init(_owner, _entryPoint, _handler);
60:         isAccountExist[proxy] = true;
61:     }
```

### L-03 Always check important state vars before update
At the moment the update for entryPoint is not checked in the constructor and not in the update function to not be the zero address. This can lead to unexpected behavior. You should add the require(_entryPoint != address(0)) check.

```solidity
File: contracts/smart-contract-wallet/paymasters/BasePaymaster.sol
20:     constructor(IEntryPoint _entryPoint) {
21:         setEntryPoint(_entryPoint);
22:     }

File: contracts/smart-contract-wallet/paymasters/BasePaymaster.sol
24:     function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
25:         entryPoint = _entryPoint;
26:     }

```

### L-04 Deviating interfaces to current EIP-4337
Some interfaces deviate to the current EIP-4337 https://eips.ethereum.org/EIPS/eip-4337.

```solidity
File: contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol
123:     error SimulationResult(uint256 preOpGas, uint256 prefund, uint256 deadline,
124:         StakeInfo senderInfo, StakeInfo factoryInfo, StakeInfo paymasterInfo);
125:     // @audit-info unknown definition, should be
126:     // error ValidationResult(ReturnInfo returnInfo, StakeInfo senderInfo, StakeInfo factoryInfo, StakeInfo paymasterInfo);

File: contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol
140:     error SimulationResultWithAggregation(uint256 preOpGas, uint256 prefund, uint256 deadline,
141:         StakeInfo senderInfo, StakeInfo factoryInfo, StakeInfo paymasterInfo, AggregatorStakeInfo aggregatorInfo);
142:     // @audit-info unknown definition, should be
143:     // error ValidationResultWithAggregation(ReturnInfo returnInfo, StakeInfo senderInfo, StakeInfo factoryInfo, StakeInfo paymasterInfo, AggregatorStakeInfo aggregatorInfo);

File: contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol
145:     // @audit-info missing completly
146:     /*
147:         struct ReturnInfo {
148:             uint256 preOpGas;
149:             uint256 prefund;
150:             bool sigFailed;
151:             uint64 validAfter;
152:             uint64 validUntil;
153:             bytes paymasterContext;
154:         }
155:     */

File: contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol
26:     function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 userOpHash, uint256 maxCost)
27:     external returns (bytes memory context, uint256 deadline); 
28:     // @audit-info return is not deadline, its a sigTimeRange that is a uint256 and holds the information for
29:     // sigFailure + validUntil + validAfter

File: contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol
24:     function validateUserOp(UserOperation calldata userOp, bytes32 userOpHash, address aggregator, uint256 missingAccountFunds)
25:     external returns (uint256 deadline);
26:     // @audit-info return is not deadline, its a sigTimeRange that is a uint256 and holds the information for
27:     // sigFailure + validUntil + validAfter

```

### L-05 Return value not checked
The return value from function _validatePrepayment is not checked and can lead to unexpected behaviour.

```solidity
File: EntryPoint.sol
75:             _validatePrepayment(i, ops[i], opInfos[i], address(0));

File: EntryPoint.sol
113:                 _validatePrepayment(opIndex, ops[i], opInfos[opIndex], address(aggregator));

```

### L-06 Inconsistent minimum stake delay
At the moment there is no minimum stake-delay and so you can add a stake with a delay of only 1 second. You can always withdraw your stake after only 1 second. Consider adding a minimum value to check against.

```solidity
File: StakeManager.sol
59:     function addStake(uint32 _unstakeDelaySec) public payable {
60:         DepositInfo storage info = deposits[msg.sender];
61:         require(_unstakeDelaySec > 0, "must specify unstake delay");
62:         require(_unstakeDelaySec >= info.unstakeDelaySec, "cannot decrease unstake time");
63:         uint256 stake = info.stake + msg.value;
64:         require(stake > 0, "no stake specified");
65:         require(stake < type(uint112).max, "stake overflow");
66:         deposits[msg.sender] = DepositInfo(
67:             info.deposit,
68:             true,
69:             uint112(stake),
70:             _unstakeDelaySec,
71:             0
72:         );
73:         emit StakeLocked(msg.sender, stake, _unstakeDelaySec);
74:     }
```

---

## Non-critical Issues

### NC-01 Remove unnecessary _requireFromEntryPointOrOwner() check in function
The execute and executeBatch call the _requireFromEntryPointOrOwner() function to check if the caller is the EntryPoint or Owner, but the function has the modifier onlyOwner and so the check is not needed.

```solidity
File: SmartAccount.sol
460:     function execute(address dest, uint value, bytes calldata func) external onlyOwner{
461:         _requireFromEntryPointOrOwner();
462:         _call(dest, value, func);
463:     }

File: SmartAccount.sol
465:     function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
466:         _requireFromEntryPointOrOwner();
467:         require(dest.length == func.length, "wrong array lengths");
468:         for (uint i = 0; i < dest.length;) {
469:             _call(dest[i], 0, func[i]);
470:             unchecked {
471:                 ++i;
472:             }
473:         }
474:     }

File: SmartAccount.sol
494:     function _requireFromEntryPointOrOwner() internal view {
495:         require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
496:     }

```

### NC-02 Remove unused fields in event or change logic
The Proxy contract emits the Received event that has the bytes data field that is always the same. You should remove it from the event or emit msg.value instead of "".

```solidity
File: contracts/smart-contract-wallet/Proxy.sol
13:     event Received(uint indexed value, address indexed sender, bytes data); 

File: contracts/smart-contract-wallet/Proxy.sol
36:     receive() external payable {
37:         emit Received(msg.value, msg.sender, "");
38:     }
```

### NC-03 Remove unused variable names from functions
If a variable is not used in a function, you can remove it in the function declaration.

```solidity
File: contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol
24:     function paymasterContext(
25:         UserOperation calldata op, // @audit-info "op" can be removed, not used
26:         PaymasterData memory data
27:     ) internal pure returns (bytes memory context) {
28:         return abi.encode(data.paymasterId);
29:     }
```

### NC-04 Wrong handling of unused variables in function
In VerifyingSingletonPaymaster in two functions you just call a variable via (var) to silence the compiler warning. You can just remove the variable in the function declaration like you did with **, bytes32 /*userOpHash*/,** and remove the (var) in the function.

```solidity
File: contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
99:         (requiredPreFund);

File: contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
124:     (mode);

```

### NC-05 Inconsistent solidity version
Should be exactly same in all contracts. Currently some contracts use ^0.8.12 but some are fixed to 0.8.12.

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol
6: pragma solidity ^0.8.12;

File: contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol
2: pragma solidity ^0.8.12;

File: contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol
2: pragma solidity ^0.8.12;

File: contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol
2: pragma solidity ^0.8.12;

File: contracts/smart-contract-wallet/aa-4337/utils/Exec.sol
2: pragma solidity >=0.7.5 <0.9.0;
```

### NC-06 Update pragma versioning to a more recent version like 0.8.16
Consider a newer version to take advantage of some bug fixes to the compiler.
https://docs.soliditylang.org/en/v0.8.17/bugs.html

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol
6: pragma solidity ^0.8.12;

File: contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol
2: pragma solidity ^0.8.12;

File: contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol
2: pragma solidity ^0.8.12;

File: contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/aa-4337/utils/Exec.sol
2: pragma solidity >=0.7.5 <0.9.0;

File: contracts/smart-contract-wallet/base/Executor.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/base/FallbackManager.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/base/ModuleManager.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/common/Enum.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/common/SignatureDecoder.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/common/Singleton.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/libs/LibAddress.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/libs/Math.sol
4: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/libs/MultiSend.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/BaseSmartAccount.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/Proxy.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/SmartAccount.sol
2: pragma solidity 0.8.12;

File: contracts/smart-contract-wallet/SmartAccountFactory.sol
2: pragma solidity 0.8.12;

```

### NC-07 Use specific imports instead of just a global import
For a better better developer experience it's better to use specific imports instead of just a global import. This helps to have a better overview what is realy needed and helps to have a clearer view of the contract.

```solidity
File: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol
12: import "../interfaces/IAccount.sol";
13: import "../interfaces/IPaymaster.sol";
15: import "../interfaces/IAggregatedAccount.sol";
16: import "../interfaces/IEntryPoint.sol";
17: import "../utils/Exec.sol";
18: import "./StakeManager.sol";
19: import "./SenderCreator.sol";

File: contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol
4: import "../interfaces/IStakeManager.sol";

File: contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol
4: import "./UserOperation.sol";

File: contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol
4: import "./UserOperation.sol";
5: import "./IAccount.sol";
6: import "./IAggregator.sol";

File: contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol
4: import "./UserOperation.sol";

File: contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol
12: import "./UserOperation.sol";
13: import "./IStakeManager.sol";
14: import "./IAggregator.sol";

File: contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol
4: import "./UserOperation.sol";

File: contracts/smart-contract-wallet/base/Executor.sol
4: import "../common/Enum.sol";

File: contracts/smart-contract-wallet/base/FallbackManager.sol
4: import "../common/SelfAuthorized.sol";

File: contracts/smart-contract-wallet/base/ModuleManager.sol
4: import "../common/Enum.sol";
5: import "../common/SelfAuthorized.sol";
6: import "./Executor.sol";

File: contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol
4: import "../interfaces/ERC1155TokenReceiver.sol";
5: import "../interfaces/ERC721TokenReceiver.sol";
6: import "../interfaces/ERC777TokensRecipient.sol";
7: import "../interfaces/IERC165.sol";

File: contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
5: import "../../BasePaymaster.sol";
6: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
7: import "@openzeppelin/contracts/proxy/utils/Initializable.sol";
8: import "@openzeppelin/contracts/utils/Address.sol";
9: import "../../PaymasterHelpers.sol";

File: contracts/smart-contract-wallet/paymasters/BasePaymaster.sol
7: import "@openzeppelin/contracts/access/Ownable.sol";
8: import "@account-abstraction/contracts/interfaces/IPaymaster.sol";
9: import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";

File: contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol
4: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
5: import "@account-abstraction/contracts/interfaces/UserOperation.sol";

File: contracts/smart-contract-wallet/BaseSmartAccount.sol
08: import "@account-abstraction/contracts/interfaces/IAccount.sol";
09: import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";
10: import "./common/Enum.sol";

File: contracts/smart-contract-wallet/SmartAccount.sol
03: import "./libs/LibAddress.sol";
04: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
05: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
06: import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
07: import "./BaseSmartAccount.sol";
08: import "./common/Singleton.sol";
09: import "./base/ModuleManager.sol";
10: import "./base/FallbackManager.sol";
11: import "./common/SignatureDecoder.sol";
12: import "./common/SecuredTokenTransfer.sol";
13: import "./interfaces/ISignatureValidator.sol";
14: import "./interfaces/IERC165.sol";
15: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

File: contracts/smart-contract-wallet/SmartAccountFactory.sol
4: import "./Proxy.sol";
5: import "./BaseSmartAccount.sol"; 
```

### NC-08 Long lines
Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol
28:     event UserOperationEvent(bytes32 indexed userOpHash, address indexed sender, address indexed paymaster, uint256 nonce, bool success, uint256 actualGasCost, uint256 actualGasUsed);

File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol
16:      * @param paymasterAndData if set, this field hold the paymaster address and "paymaster-specific-data". the paymaster will pay for the transaction instead of the sender

File: scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol
10:         @dev An ERC1155-compliant smart contract MUST call this function on the token recipient contract, at the end of a `safeTransferFrom` after the balance has been updated.

File: scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol
31:         @dev An ERC1155-compliant smart contract MUST call this function on the token recipient contract, at the end of a `safeBatchTransferFrom` after the balances have been updated.

File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
44:     // review? if rename wallet to account is must
45:     // keccak256(
46:     //     "AccountTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"
47:     // );

File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
239:                 payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);

File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
489:     function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        
```

### NC-09 Add missing documentation
The contracts are good documented and clear to read but not in an expected NatSpec way. For a better developer experience you should try to follow the NatSpec-Documentation for all public functions. 

Functions missing @param (or wrong param mentioned) / @return / @notice or is missing complete
```solidity
File: BaseSmartAccount.sol
41:     function nonce() public view virtual returns (uint256);

File: BaseSmartAccount.sol
47:     function nonce(uint256 _batchId) public view virtual returns (uint256);

File: BaseSmartAccount.sol
53:     function entryPoint() public view virtual returns (IEntryPoint);

File: BaseSmartAccount.sol
60:     function validateUserOp(UserOperation calldata userOp, bytes32 userOpHash, address aggregator, uint256 missingAccountFunds)
61:     external override virtual returns (uint256 deadline) {

File: BaseSmartAccount.sol
114:     function init(address _owner, address _entryPointAddress, address _handler) external virtual;

File: BaseSmartAccount.sol
116:     function execTransaction(
117:         Transaction memory _tx,
118:         uint256 batchId,
119:         FeeRefund memory refundInfo,
120:         bytes memory signatures) public payable virtual returns (bool success);

File: EntryPoint.sol
168:     function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost) {

File: EntryPoint.sol
196:     function getUserOpHash(UserOperation calldata userOp) public view returns (bytes32) {

File: ERC777TokensRecipient.sol
5:     function tokensReceived(

File: IAggregatedAccount.sol
17:     function getAggregator() external view returns (address);

File: IERC165.sol
14:     function supportsInterface(bytes4 interfaceId) external view returns (bool);

File: ISignatureValidator.sol
19:     function isValidSignature(bytes memory _data, bytes memory _signature) public view virtual returns (bytes4);

File: IStakeManager.sol
67:     function getDepositInfo(address account) external view returns (DepositInfo memory info);

File: IStakeManager.sol
70:     function balanceOf(address account) external view returns (uint256);

File: IStakeManager.sol
75:     function depositTo(address account) external payable;

File: ModuleManager.sol
61:     function execTransactionFromModule(
62:         address to,
63:         uint256 value,
64:         bytes memory data,
65:         Enum.Operation operation
66:     ) public virtual returns (bool success) {

File: ModuleManager.sol
80:     function execTransactionFromModuleReturnData(
81:         address to,
82:         uint256 value,
83:         bytes memory data,
84:         Enum.Operation operation
85:     ) public returns (bool success, bytes memory returnData) {

File: ModuleManager.sol
105:     function isModuleEnabled(address module) public view returns (bool) {

File: SmartAccount.sol
93:    function nonce() public view virtual override returns (uint256) {

File: SmartAccount.sol
97:     function nonce(uint256 _batchId) public view virtual override returns (uint256) {

File: SmartAccount.sol
101:     function entryPoint() public view virtual override returns (IEntryPoint) {

File: SmartAccount.sol
109:     function setOwner(address _newOwner) external mixedAuth {

File: SmartAccount.sol
127:     function updateEntryPoint(address _newEntryPoint) external mixedAuth {

File: SmartAccount.sol
135:     function domainSeparator() public view returns (bytes32) {

File: SmartAccount.sol
140:     function getChainId() public view returns (uint256) {

File: SmartAccount.sol
166:     function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 

File: SmartAccount.sol
192:     function execTransaction(
193:         Transaction memory _tx,
194:         uint256 batchId,
195:         FeeRefund memory refundInfo,
196:         bytes memory signatures
197:     ) public payable virtual override returns (bool success) {

File: SmartAccount.sol
271:     function handlePaymentRevert(
272:         uint256 gasUsed,
273:         uint256 baseGas,
274:         uint256 gasPrice,
275:         uint256 tokenGasPriceFactor,
276:         address gasToken,
277:         address payable refundReceiver
278:     ) external returns (uint256 payment) {

File: SmartAccount.sol
302:     function checkSignatures(
303:         bytes32 dataHash,
304:         bytes memory data,
305:         bytes memory signatures
306:     ) public view virtual {

File: SmartAccount.sol
389:     function getTransactionHash(
390:         address to,
391:         uint256 value,
392:         bytes calldata data,
393:         Enum.Operation operation,
394:         uint256 targetTxGas,
395:         uint256 baseGas,
396:         uint256 gasPrice,
397:         uint256 tokenGasPriceFactor,
398:         address gasToken,
399:         address payable refundReceiver,
400:         uint256 _nonce
401:     ) public view returns (bytes32) {

File: SmartAccount.sol
449:     function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {

File: SmartAccount.sol
455:     function pullTokens(address token, address dest, uint256 amount) external onlyOwner {

File: SmartAccount.sol
460:     function execute(address dest, uint value, bytes calldata func) external onlyOwner{

File: SmartAccount.sol
465:     function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{

File: SmartAccount.sol
489:     function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        

File: SmartAccount.sol
518:     function getDeposit() public view returns (uint256) {

File: SmartAccountFactory.sol
33:     function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address proxy){

File: SmartAccountFactory.sol
53:     function deployWallet(address _owner, address _entryPoint, address _handler) public returns(address proxy){ 

File: SmartAccountFactory.sol
68:     function getAddressForCounterfactualWallet(address _owner, uint _index) external view returns (address _wallet) {
```

### NC-10 Typos
There are some typos.

```solidity
File: Exec.sol
39:     // get returned data from last call or calldelegate // @audit-info calldelegate != delegatecall

File: IAggregatedAccount.sol
10:  * - the validateUserOp MUST valiate the aggregator parameter, and MAY ignore the userOp.signature field. // @audit-info valiate != validate

File: SmartAccount.sol
228:             // We only substract 2500 (compared to the 3000 before) to ensure that the amount passed is still higher than targetTxGas // @audit-info substract != subtract

```