## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::234 => uint256 payment = 0;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::310 => uint256 i = 0;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::468 => for (uint i = 0; i < dest.length;) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::234 => uint256 payment = 0;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::305 => uint256 i = 0;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::458 => for (uint i = 0; i < dest.length;) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::119 => uint256 moduleCount = 0;
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::468 => for (uint i = 0; i < dest.length;) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::458 => for (uint i = 0; i < dest.length;) {
```

## [G-03] Long Revert Strings

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::18 => require(_baseImpl != address(0), "base wallet address can not be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
```

## [G-04] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::198 => // initial gas = 21k + non_zero_bytes * 16 + zero_bytes * 4
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::198 => // initial gas = 21k + non_zero_bytes * 16 + zero_bytes * 4
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::199 => //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
```

## [G-05] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::8 => address immutable public _defaultImpl;
```

## [G-06] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::455 => function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::460 => function execute(address dest, uint value, bytes calldata func) external onlyOwner{
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::465 => function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::489 => function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::445 => function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::450 => function execute(address dest, uint value, bytes calldata func) external onlyOwner{
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::455 => function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::479 => function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {
```

## [G-07] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::307 => uint8 v;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::302 => uint8 v;
```

## [G-08] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::15 => mapping (address => bool) public isAccountExist;
```

## [G-09] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::216 => nonces[batchId]++;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::471 => ++i;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::502 => require(nonces[0]++ == userOp.nonce, "account: invalid nonce");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::216 => nonces[batchId]++;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::461 => ++i;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::492 => require(nonces[0]++ == userOp.nonce, "account: invalid nonce");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::124 => moduleCount++;
```

## [G-10] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::136 => return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::431 => abi.encode(
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::136 => return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::421 => abi.encode(
```

## [G-11] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol::74 => require(msg.sender == address(entryPoint()), "account: not from EntryPoint");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::83 => require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::89 => require(msg.sender == address(entryPoint()), "wallet: not from EntryPoint");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::121 => require(_implementation.isContract(), "INVALID_IMPLEMENTATION");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::167 => require(owner == address(0), "Already initialized");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::168 => require(address(_entryPoint) == address(0), "Already initialized");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::169 => require(_owner != address(0),"Invalid owner");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::170 => require(_entryPointAddress != address(0), "Invalid Entrypoint");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::171 => require(_handler != address(0), "Invalid Entrypoint");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::224 => require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::232 => require(success || _tx.targetTxGas != 0 || refundInfo.gasPrice != 0, "BSA013");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::262 => require(success, "BSA011");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::265 => require(transferToken(gasToken, receiver, payment), "BSA012");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::286 => require(success, "BSA011");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::289 => require(transferToken(gasToken, receiver, payment), "BSA012");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::322 => require(uint256(s) >= uint256(1) * 65, "BSA021");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::325 => require(uint256(s) + 32 <= signatures.length, "BSA022");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::333 => require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::342 => require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::348 => require(_signer == owner, "INVALID_SIGNATURE");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::351 => require(_signer == owner, "INVALID_SIGNATURE");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::450 => require(dest != address(0), "this action will burn your funds");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::452 => require(success,"transfer failed");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::467 => require(dest.length == func.length, "wrong array lengths");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::491 => require(success, "Userop Failed");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::495 => require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::502 => require(nonces[0]++ == userOp.nonce, "account: invalid nonce");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::511 => require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::18 => require(_baseImpl != address(0), "base wallet address can not be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::40 => require(address(proxy) != address(0), "Create2 call failed");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::83 => require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::89 => require(msg.sender == address(entryPoint()), "wallet: not from EntryPoint");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::121 => require(_implementation.isContract(), "INVALID_IMPLEMENTATION");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::167 => require(owner == address(0), "Already initialized");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::168 => require(address(_entryPoint) == address(0), "Already initialized");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::169 => require(_owner != address(0),"Invalid owner");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::170 => require(_entryPointAddress != address(0), "Invalid Entrypoint");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::171 => require(_handler != address(0), "Invalid Entrypoint");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::224 => require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::232 => require(success || _tx.targetTxGas != 0 || refundInfo.gasPrice != 0, "BSA013");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::262 => require(success, "BSA011");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::265 => require(transferToken(gasToken, receiver, payment), "BSA012");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::285 => require(success, "BSA011");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::288 => require(transferToken(gasToken, receiver, payment), "BSA012");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::317 => require(uint256(s) >= uint256(1) * 65, "BSA021");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::320 => require(uint256(s) + 32 <= signatures.length, "BSA022");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::328 => require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::337 => require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::343 => require(_signer == owner || true, "INVALID_SIGNATURE");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::346 => require(_signer == owner || true, "INVALID_SIGNATURE");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::440 => require(dest != address(0), "this action will burn your funds");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::442 => require(success,"transfer failed");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::457 => require(dest.length == func.length, "wrong array lengths");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::481 => require(success, "Userop Failed");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::485 => require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::492 => require(nonces[0]++ == userOp.nonce, "account: invalid nonce");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::501 => require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::21 => require(modules[SENTINEL_MODULES] == address(0), "BSA100");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::25 => require(execute(to, 0, data, Enum.Operation.DelegateCall, gasleft()), "BSA000");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::34 => require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::36 => require(modules[module] == address(0), "BSA102");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::49 => require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::50 => require(modules[prevModule] == module, "BSA103");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::68 => require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```

## [G-12] Use bytes32 instead of string

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::36 => string public constant VERSION = "1.0.2"; // using AA 0.3.0
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::11 => string public constant VERSION = "1.0.2";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::36 => string public constant VERSION = "1.0.2"; // aa 0.3.0 rebase
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::12 => string public constant NAME = "Default Callback Handler";
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::13 => string public constant VERSION = "1.0.0";
```

## [G-13] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::34 => require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::49 => require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::68 => require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```

## [G-14] Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas.
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```

## [G-15] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::166 => function init(address _owner, address _entryPointAddress, address _handler) public override initializer {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::518 => function getDeposit() public view returns (uint256) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::525 => function addDeposit() public payable {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::536 => function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::33 => function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address proxy){
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol::53 => function deployWallet(address _owner, address _entryPoint, address _handler) public returns(address proxy){
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::166 => function init(address _owner, address _entryPointAddress, address _handler) public override initializer {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::508 => function getDeposit() public view returns (uint256) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::515 => function addDeposit() public payable {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::526 => function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol::26 => function setFallbackHandler(address handler) public authorized {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::32 => function enableModule(address module) public authorized {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::47 => function disableModule(address prevModule, address module) public authorized {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::105 => function isModuleEnabled(address module) public view returns (bool) {
```

## [G-16] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::507 => internal override virtual returns (uint256 deadline) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::497 => internal override virtual returns (uint256 deadline) {
```

## [G-17] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol::73 => function _requireFromEntryPoint() internal virtual view {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol::96 => function _validateAndUpdateNonce(UserOperation calldata userOp) internal virtual;
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol::106 => function _payPrefund(uint256 missingAccountFunds) internal virtual {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol::181 => function max(uint256 a, uint256 b) internal pure returns (uint256) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::181 => function max(uint256 a, uint256 b) internal pure returns (uint256) {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol::14 => function internalSetFallbackHandler(address handler) internal {
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::20 => function setupModules(address to, bytes memory data) internal {
```

## [G-18] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol::20 => function setupModules(address to, bytes memory data) internal {
```