

### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::51 => address public owner;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::59 => IEntryPoint private _entryPoint;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::51 => address public owner;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::59 => IEntryPoint private _entryPoint;
```



### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::51 => address public owner;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::51 => address public owner;
```



### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::468 => for (uint i = 0; i < dest.length;) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::458 => for (uint i = 0; i < dest.length;) {
```



### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/Strings.sol::56 => for (uint256 i = 2 * length + 1; i > 1; --i) {
```



### [G05] `require()`/`revert()` strings longer than 32 bytes cost extra gas


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::18 => require(_baseImpl != address(0), "base wallet address can not be zero");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
2023-01-biconomy-main/contracts/smart-contract-wallet/common/Singleton.sol::13 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/MultiSend.sol::27 => require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");
```



### [G06] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::102 => return _entryPoint;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::102 => return _entryPoint;
```



### [G07] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::234 => uint256 payment = 0;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::310 => uint256 i = 0;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::468 => for (uint i = 0; i < dest.length;) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::234 => uint256 payment = 0;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::305 => uint256 i = 0;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::458 => for (uint i = 0; i < dest.length;) {
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::119 => uint256 moduleCount = 0;
```


### [G08] Splitting `require()` statements that use `&&` Cost gas

#### Impact
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) for an example
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::34 => require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::49 => require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::68 => require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```



### [G09] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::53 => // uint96 private _nonce; //changed to 2D nonce below
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::307 => uint8 v;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::34 => bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::35 => bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::54 => bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::68 => function getAddressForCounterfactualWallet(address _owner, uint _index) external view returns (address _wallet) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::69 => bytes memory code = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::70 => bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::72 => _wallet = address(uint160(uint(hash)));
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::302 => uint8 v;
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/Strings.sol::13 => uint8 private constant _ADDRESS_LENGTH = 20;
```


### [G10] Use custom errors rather than `revert()`/`require()` strings to save deployment gas


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/BaseSmartAccount.sol::74 => require(msg.sender == address(entryPoint()), "account: not from EntryPoint");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::83 => require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::89 => require(msg.sender == address(entryPoint()), "wallet: not from EntryPoint");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::121 => require(_implementation.isContract(), "INVALID_IMPLEMENTATION");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::167 => require(owner == address(0), "Already initialized");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::168 => require(address(_entryPoint) == address(0), "Already initialized");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::169 => require(_owner != address(0),"Invalid owner");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::170 => require(_entryPointAddress != address(0), "Invalid Entrypoint");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::171 => require(_handler != address(0), "Invalid Entrypoint");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::224 => require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::232 => require(success || _tx.targetTxGas != 0 || refundInfo.gasPrice != 0, "BSA013");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::262 => require(success, "BSA011");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::265 => require(transferToken(gasToken, receiver, payment), "BSA012");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::286 => require(success, "BSA011");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::289 => require(transferToken(gasToken, receiver, payment), "BSA012");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::322 => require(uint256(s) >= uint256(1) * 65, "BSA021");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::325 => require(uint256(s) + 32 <= signatures.length, "BSA022");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::333 => require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::342 => require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::348 => require(_signer == owner, "INVALID_SIGNATURE");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::351 => require(_signer == owner, "INVALID_SIGNATURE");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::450 => require(dest != address(0), "this action will burn your funds");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::452 => require(success,"transfer failed");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::467 => require(dest.length == func.length, "wrong array lengths");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::491 => require(success, "Userop Failed");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::495 => require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::502 => require(nonces[0]++ == userOp.nonce, "account: invalid nonce");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::511 => require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::18 => require(_baseImpl != address(0), "base wallet address can not be zero");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::40 => require(address(proxy) != address(0), "Create2 call failed");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::77 => require(msg.sender == owner, "Smart Account:: Sender is not authorized");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::83 => require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::89 => require(msg.sender == address(entryPoint()), "wallet: not from EntryPoint");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::110 => require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::121 => require(_implementation.isContract(), "INVALID_IMPLEMENTATION");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::128 => require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::167 => require(owner == address(0), "Already initialized");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::168 => require(address(_entryPoint) == address(0), "Already initialized");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::169 => require(_owner != address(0),"Invalid owner");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::170 => require(_entryPointAddress != address(0), "Invalid Entrypoint");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::171 => require(_handler != address(0), "Invalid Entrypoint");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::224 => require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::232 => require(success || _tx.targetTxGas != 0 || refundInfo.gasPrice != 0, "BSA013");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::262 => require(success, "BSA011");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::265 => require(transferToken(gasToken, receiver, payment), "BSA012");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::285 => require(success, "BSA011");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::288 => require(transferToken(gasToken, receiver, payment), "BSA012");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::317 => require(uint256(s) >= uint256(1) * 65, "BSA021");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::320 => require(uint256(s) + 32 <= signatures.length, "BSA022");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::328 => require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::337 => require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::343 => require(_signer == owner || true, "INVALID_SIGNATURE");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::346 => require(_signer == owner || true, "INVALID_SIGNATURE");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::440 => require(dest != address(0), "this action will burn your funds");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::442 => require(success,"transfer failed");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::457 => require(dest.length == func.length, "wrong array lengths");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::471 => revert(add(result, 32), mload(result))
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::481 => require(success, "Userop Failed");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::484 => function _requireFromEntryPointOrOwner() internal view {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::485 => require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::492 => require(nonces[0]++ == userOp.nonce, "account: invalid nonce");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::501 => require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::21 => require(modules[SENTINEL_MODULES] == address(0), "BSA100");
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::25 => require(execute(to, 0, data, Enum.Operation.DelegateCall, gasleft()), "BSA000");
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::34 => require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::36 => require(modules[module] == address(0), "BSA102");
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::49 => require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::50 => require(modules[prevModule] == module, "BSA103");
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::68 => require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/MultiSend.sol::27 => require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/Strings.sol::60 => require(value == 0, "Strings: hex length insufficient");
```



### [G11] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::449 => function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::455 => function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::460 => function execute(address dest, uint value, bytes calldata func) external onlyOwner{
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::465 => function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::489 => function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::536 => function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::439 => function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::445 => function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::450 => function execute(address dest, uint value, bytes calldata func) external onlyOwner{
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::455 => function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::479 => function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::526 => function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
```



### [G12] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done in some places, it’s not consistently done in the solution.
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::457 => SafeERC20.safeTransfer(tokenContract, dest, amount);
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::447 => SafeERC20.safeTransfer(tokenContract, dest, amount);
```




### [G13] Remove unused local variable


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/BaseSmartAccount.sol::108 => (bool success,) = payable(msg.sender).call{value : missingAccountFunds, gas : type(uint256).max}("");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::197 => ) public payable virtual override returns (bool success) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::261 => (bool success,) = receiver.call{value: payment}("");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::285 => (bool success,) = receiver.call{value: payment}("");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::451 => (bool success,) = dest.call{value:amount}("");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::527 => (bool req,) = address(entryPoint()).call{value : msg.value}("");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::261 => (bool success,) = receiver.call{value: payment}("");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::284 => (bool success,) = receiver.call{value: payment}("");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::441 => (bool success,) = dest.call{value:amount}("");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::517 => (bool req,) = address(entryPoint()).call{value : msg.value}("");
```



### [G14] Using `bools` for storage incurs overhead


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::15 => mapping (address => bool) public isAccountExist;
```





### [G15] Using `private` rather than `public` for constants, saves gas


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::36 => string public constant VERSION = "1.0.2"; // using AA 0.3.0
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::11 => string public constant VERSION = "1.0.2";
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::36 => string public constant VERSION = "1.0.2"; // aa 0.3.0 rebase
2023-01-biconomy-main/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::12 => string public constant NAME = "Default Callback Handler";
2023-01-biconomy-main/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol::13 => string public constant VERSION = "1.0.0";
```



### [G16] Usage of `assert()` instead of `require()`


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
2023-01-biconomy-main/contracts/smart-contract-wallet/common/Singleton.sol::13 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```



### [G17] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::550 => receive() external payable {}
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::540 => receive() external payable {}
```




### [G18] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::136 => return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::431 => abi.encode(
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::421 => abi.encode(
```
