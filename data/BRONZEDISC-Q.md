## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

```solidity
// event coming after constructor
24:    event SmartAccountCreated(address indexed _proxy, address indexed _implementation, address indexed _owner, string version, uint256 _index);
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestCounter.sol

```solidity
// events is coming after functions
17:    event CalledFrom(address sender);

// state vars are coming after functions
22:    mapping(uint256 => uint256) public xxx;
23:    uint256 public offset;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```solidity
//  structs should come before functions
145:    struct MemoryUserOp {
156:    struct UserOpInfo {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

```solidity
// these state variables should come before events
16:    address internal constant SENTINEL_MODULES = address(0x1);
18:    mapping(address => address) internal modules;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SelfAuthorized.sol

```solidity
// modifiers should come before functions
10:    modifier authorized() {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/test/Button.sol

```solidity
// events should come after state variables
7:   event ButtonPushed(address pusher, uint256 pushes);
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol

```solidity
// some external functions coming after internal ones
109:    function setOwner(address _newOwner) external mixedAuth {
120:    function updateImplementation(address _implementation) external mixedAuth {
127:    function updateEntryPoint(address _newEntryPoint) external mixedAuth {
271:    function handlePaymentRevert(
357:    function requiredTxGas(
439:    function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
445:    function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
450:    function execute(address dest, uint value, bytes calldata func) external onlyOwner{
455:    function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
479:    function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        
535:    function supportsInterface(bytes4 interfaceId) external view virtual override returns (bool) {

// internal functions coming before public and external ones
181:    function max(uint256 a, uint256 b) internal pure returns (uint256) {
467:    function _call(address target, uint256 value, bytes memory data) internal {
484:    function _requireFromEntryPointOrOwner() internal view {
491:    function _validateAndUpdateNonce(UserOperation calldata userOp) internal override {
496:    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```solidity
  The whole file has mixed visibility functions, the dev should consider ordering them acording tospecifications 
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

```solidity
  The whole file has mixed visibility functions, the dev should consider ordering them acording tospecifications 
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccount.sol

```solidity
// internal function coming before public and external ones
27:    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address userOpAggregator)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol

```solidity
// internal functions coming before public and external ones
52:     function _onlyOwner() internal view {
85:     function _initialize(address anOwner) internal virtual {
98:     function _requireFromEntryPointOrOwner() internal view {
108:    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)
117:    function _call(address target, uint256 value, bytes memory data) internal {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol

```solidity
// var coming after functions
36:    IEntryPoint private immutable _entryPoint;

// event coming after functions
38:    event SimpleAccountInitialized(IEntryPoint indexed entryPoint, address indexed owner);

// receive() should come after constructor
41:    receive() external payable {}

// modifier coming after constructor
47:    modifier onlyOwner() {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

```solidity
23:    function getStakeInfo(address addr) internal view returns (StakeInfo memory info) {

38:    function internalIncrementDeposit(address account, uint256 amount) internal {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol

```solidity
// internal function coming before public and external ones
48:    function _postOp(PostOpMode mode, bytes calldata context, uint256 actualGasCost) internal virtual {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

```solidity
// internal function coming before public and external ones
20:    function setupModules(address to, bytes memory data) internal {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

```solidity
  The whole file has mixed visibility functions, the dev should consider ordering them acording tospecifications 
```

---

### natSpec missing [3].

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol

```solidity
18:   contract SmartAccountNoAuth is 

93:   function nonce() public view virtual override returns (uint256) {

97:    function nonce(uint256 _batchId) public view virtual override returns (uint256) {

101:    function entryPoint() public view virtual override returns (IEntryPoint) {

109:    function setOwner(address _newOwner) external mixedAuth {

127:    function updateEntryPoint(address _newEntryPoint) external mixedAuth {

135:    function domainSeparator() public view returns (bytes32) {

166:    function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 

181:    function max(uint256 a, uint256 b) internal pure returns (uint256) {

247:    function handlePayment(

271:    function handlePaymentRevert(

439:    function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {

445:    function pullTokens(address token, address dest, uint256 amount) external onlyOwner {

450:    function execute(address dest, uint value, bytes calldata func) external onlyOwner{

455:    function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{

467:    function _call(address target, uint256 value, bytes memory data) internal {

479:    function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        

484:    function _requireFromEntryPointOrOwner() internal view {

496:    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```solidity
18:  contract SmartAccount is 

93:   function nonce() public view virtual override returns (uint256) {

97:    function nonce(uint256 _batchId) public view virtual override returns (uint256) {

101:   function entryPoint() public view virtual override returns (IEntryPoint) {

109:    function setOwner(address _newOwner) external mixedAuth {

127:    function updateEntryPoint(address _newEntryPoint) external mixedAuth {

135:    function domainSeparator() public view returns (bytes32) {

166:    function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 

247:    function handlePayment(

271:    function handlePaymentRevert(

449:    function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {

455:    function pullTokens(address token, address dest, uint256 amount) external onlyOwner {

460:    function execute(address dest, uint value, bytes calldata func) external onlyOwner{

465:    function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{

477:    function _call(address target, uint256 value, bytes memory data) internal {

//incomplete
489:    function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        

494:    function _requireFromEntryPointOrOwner() internal view {

//incomplete
506:    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)


```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol

```solidity
15:    constructor(address _implementation) {

22:    fallback() external payable {

36:    receive() external payable {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

```solidity
114:    function init(address _owner, address _entryPointAddress, address _handler) external virtual;

116:    function execTransaction(
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol

```solidity
8:    function call(

19:    function staticcall(

29:    function delegateCall(

40:    function getReturnData() internal pure returns (bytes memory returnData) {

51:    function revertWithData(bytes memory returnData) internal pure {

57:    function callAndRevert(address to, bytes memory data) internal {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestUtil.sol

```solidity
6:  contract TestUtil {

9:    function packUserOp(UserOperation calldata op) external pure returns (bytes memory){
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestToken.sol

```solidity
6:  contract TestToken is ERC20 {

7:    constructor ()

12:    function mint(address sender, uint256 amount) external {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestPaymasterAcceptAll.sol

```solidity
11:    constructor(IEntryPoint _entryPoint) BasePaymaster(_entryPoint) {

19:    function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 userOpHash, uint maxCost) external virtual override view
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestOracle.sol

```solidity
6:  contract TestOracle is IOracle {

7:    function getTokenValueOfEth(uint256 ethOutput) external pure override returns (uint256 tokenInput) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestExpiryAccount.sol

```solidity
18:    constructor(IEntryPoint anEntryPoint) SimpleAccount(anEntryPoint) {}

20:    function initialize(address anOwner) public virtual override initializer {

25:    function addTemporaryOwner(address owner, uint256 deadline) public onlyOwner {

30:    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestExpirePaymaster.sol

```solidity
11:    constructor(IEntryPoint _entryPoint) BasePaymaster(_entryPoint)

14:    function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 userOpHash, uint maxCost) external virtual override view
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestCounter.sol

```solidity
8:    function count() public {

13:    function justemit() public {

25:    function gasWaster(uint256 repeat, string calldata /*junk*/) external {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol

```solidity
25:    constructor(IEntryPoint _entryPoint, address _verifyingSigner) BasePaymaster(_entryPoint) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol

```solidity
17:    function validateSignatures(UserOperation[] calldata userOps, bytes calldata signature) external pure override {

29:    function validateUserOpSignature(UserOperation calldata)

37:    function aggregateSignatures(UserOperation[] calldata userOps) external pure returns (bytes memory aggregatesSignature) {

45:    function addStake(IEntryPoint entryPoint, uint32 delay) external payable {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccountFactory.sol

```solidity
17:    constructor(IEntryPoint anEntryPoint, address anAggregator){

//incomplete
42:    function getAddress(address owner, uint salt) public view returns (address) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccount.sol

```solidity
//incomplete
19:    constructor(IEntryPoint anEntryPoint, address anAggregator) SimpleAccount(anEntryPoint) {

23:    function initialize(address) public virtual override initializer {

27:    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address userOpAggregator)

34:    function getAggregator() external override view returns (address) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountForTokens.sol

```solidity
//incomplete
13:    constructor(IEntryPoint anEntryPoint) SimpleAccount(anEntryPoint) {}

15:    function initialize(address _owner, IERC20 token, address paymaster) public virtual initializer {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol

```solidity
18:    constructor(IEntryPoint _entryPoint){

//incomplete
43:    function getAddress(address owner, uint salt) public view returns (address) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol

```solidity
28:    function nonce() public view virtual override returns (uint256) {

32:    function entryPoint() public view virtual override returns (IEntryPoint) {

43:    constructor(IEntryPoint anEntryPoint) {

52:    function _onlyOwner() internal view {

60:    function execute(address dest, uint256 value, bytes calldata func) external {

//incomplete
68:    function executeBatch(address[] calldata dest, bytes[] calldata func) external {

//incomplete
81:    function initialize(address anOwner) public virtual initializer {

85:    function _initialize(address anOwner) internal virtual {

//incomplete
103:    function _validateAndUpdateNonce(UserOperation calldata userOp) internal override {

//incomplete
108:    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)

//incomplete
129:    function getDeposit() public view returns (uint256) {

151:    function _authorizeUpgrade(address newImplementation) internal view override {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/IOracle.sol

```solidity
4:  interface IOracle {

//incomplete
9:    function getTokenValueOfEth(uint256 ethOutput) external view returns (uint256 tokenInput);
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol

```solidity
40:    constructor(IEntryPoint _entryPoint) BasePaymaster(_entryPoint) {

// incomplete
48:    function addToken(IERC20 token, IOracle tokenPriceOracle) external onlyOwner {

73:    function depositInfo(IERC20 token, address account) public view returns (uint256 amount, uint256 _unlockBlock) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol

```solidity
34: library UserOperationLib {

36:    function getSender(UserOperation calldata userOp) internal pure returns (address) {

// spec incomplete
45:    function gasPrice(UserOperation calldata userOp) internal view returns (uint256) {

57:    function pack(UserOperation calldata userOp) internal pure returns (bytes memory ret) {

73:    function hash(UserOperation calldata userOp) internal pure returns (bytes32) {

77:    function min(uint256 a, uint256 b) internal pure returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

```solidity
18:    function getDepositInfo(address account) public view returns (DepositInfo memory info) {

//incomplete
23:    function getStakeInfo(address addr) internal view returns (StakeInfo memory info) {

//incomplete
30:    function balanceOf(address account) public view returns (uint256) {

34:    receive() external payable {

38:    function internalIncrementDeposit(address account, uint256 amount) internal {

//incomplete
48:    function depositTo(address account) public payable {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```solidity
21:   contract EntryPoint is IEntryPoint, StakeManager {

// spec incomplete
484:    function getUserOpGasPrice(MemoryUserOp memory mUserOp) internal view returns (uint256) {

496:    function min(uint256 a, uint256 b) internal pure returns (uint256) {

500:    function getOffsetOfMemoryBytes(bytes memory data) internal pure returns (uint256 offset) {

504:    function getMemoryBytesFromOffset(uint256 offset) internal pure returns (bytes memory data) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol

```solidity
20:    constructor(IEntryPoint _entryPoint) {

24:    function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {

28:    function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 userOpHash, uint256 maxCost)

31:    function postOp(PostOpMode mode, bytes calldata context, uint256 actualGasCost) external override {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

```solidity
20:    function setupModules(address to, bytes memory data) internal {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

```solidity
14:    function internalSetFallbackHandler(address handler) internal {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol

```solidity
// spec missing
13:    function execute(
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol

```solidity
12:    function _setImplementation(address _imp) internal {

20:    function _getImplementation() internal view returns (address _imp) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SelfAuthorized.sol

```solidity
6:    function requireSelfCall() private view {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol

```solidity
15:    function onERC1155Received(

25:    function onERC1155BatchReceived(

35:    function onERC721Received(

44:    function tokensReceived(

55:    function supportsInterface(bytes4 interfaceId) external view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

```solidity
35:    constructor(IEntryPoint _entryPoint, address _verifyingSigner) BasePaymaster(_entryPoint) {

41:    function deposit() public virtual override payable {

// incomplete spec
48:    function depositFor(address paymasterId) public payable {

55:    function withdrawTo(address payable withdrawAddress, uint256 amount) public override {

// incomplete spec
65:    function setSigner( address _newVerifyingSigner) external onlyOwner{
```


https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

```solidity
18:   library PaymasterHelpers {

// incomplete spec
24:    function paymasterContext(

// incomplete spec
34:    function decodePaymasterData(UserOperation calldata op) internal pure returns (PaymasterData memory) {

// incomplete spec
43:    function decodePaymasterContext(bytes memory context) internal pure returns (PaymasterContext memory) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

```solidity
20:    constructor(IEntryPoint _entryPoint) {

24:    function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {

28:    function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 userOpHash, uint256 maxCost)

31:    function postOp(PostOpMode mode, bytes calldata context, uint256 actualGasCost) external override {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/test/StorageSetter.sol

```solidity
3:  contract StorageSetter {

4:  function setStorage(bytes3 data) public {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/test/StakedTestToken.sol

```solidity
11:    constructor (address _token) 

16:    function mint(address sender, uint amount) external {

20:    function stake(address _for, uint amount) external {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/test/MockToken.sol

```solidity
6:  contract MockToken is ERC20 {

7:    constructor ()

11:    function mint(address sender, uint amount) external {

15:    function decimals() public view virtual override returns (uint8) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/test/Button.sol

```solidity
5:  contract Button is Ownable {

10:    function pushButton() public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/utils/GasEstimatorSmartAccount.sol

```solidity
9:  function estimate(
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/utils/GasEstimator.sol

```solidity
6:  function estimate(
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/utils/Decoder.sol

```solidity
4:  contract Decoder {

5:    function decode(address to, bytes memory data) public returns (bytes memory) {
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```solidity
181:    function max(uint256 a, uint256 b) internal pure returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

```solidity
23:    function getStakeInfo(address addr) internal view returns (StakeInfo memory info) {

38:    function internalIncrementDeposit(address account, uint256 amount) internal {
```

---

### Version [5]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity >=0.7.5 <0.9.0;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestUtil.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestToken.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestPaymasterAcceptAll.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestOracle.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestExpiryAccount.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestExpirePaymaster.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/test/TestCounter.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/VerifyingPaymaster.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/TokenPaymaster.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/TestSignatureAggregator.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/TestAggregatedAccount.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountForTokens.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccountFactory.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/IOracle.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol

```solidity
// this version differs from the rest of the repo
6:  pragma solidity ^0.8.12;
```


https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```solidity
// this version differs from the rest of the repo
6:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

```solidity
// this version differs from the rest of the repo
2:  pragma solidity ^0.8.12;
```

---

### Observations [6]

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/test/StakedTestToken.sol

```solidity
// this variable could be `immutable`
9:    address public STAKED_TOKEN;
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/utils/Decoder.sol

```solidity
// change the error message to a more specific explanation
7:  require(!success, "Shit happens");
```
