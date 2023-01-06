

### [L01] require() should be used instead of assert()


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/Proxy.sol::16 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
2023-01-biconomy-main/contracts/smart-contract-wallet/common/Singleton.sol::13 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```


### [L02] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::112 => owner = _newOwner;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::172 => owner = _owner;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::19 => _defaultImpl = _baseImpl;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::112 => owner = _newOwner;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::172 => owner = _owner;
```



### [L03] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::166 => function init(address _owner, address _entryPointAddress, address _handler) public override initializer {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::166 => function init(address _owner, address _entryPointAddress, address _handler) public override initializer {
```




### [L04] Unused `receive()` function will lock Ether in contract

#### Impact
If the intention is for the Ether to be used, the function should call another function, otherwise, it should revert
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/Proxy.sol::36 => receive() external payable {}
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::550 => receive() external payable {}
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::540 => receive() external payable {}
```


    ````
    #### Non-Critical Issues
    ````

### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::102 => return _entryPoint;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::146 => return id;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::102 => return _entryPoint;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::146 => return id;
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/Strings.sol::36 => return buffer;
```



### [N02] `require()`/`revert()` statements should have descriptive reason strings


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/Proxy.sol::31 => case 0 {revert(0, returndatasize())}
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::294 => revert(string(abi.encodePacked(requiredGas)));
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::371 => require(execute(to, value, data, operation, gasleft()));
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::374 => revert(string(abi.encodePacked(requiredGas)));
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::481 => revert(add(result, 32), mload(result))
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::528 => require(req);
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::471 => revert(add(result, 32), mload(result))
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::518 => require(req);
2023-01-biconomy-main/contracts/smart-contract-wallet/base/FallbackManager.sol::48 => revert(0, returndatasize())
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/MultiSend.sol::59 => revert(0, 0)
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::51 => revert(0, 0)
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::54 => revert(0, 0)
```



### [N03] constants should be defined rather than using magic numbers



#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::42 => bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH = 0x47e79534a245952e8b16893a336b85a3d9ea9fa8c573f3d803afb92a79469218;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::224 => require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::322 => require(uint256(s) >= uint256(1) * 65, "BSA021");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::42 => bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH = 0x47e79534a245952e8b16893a336b85a3d9ea9fa8c573f3d803afb92a79469218;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::48 => bytes32 internal constant ACCOUNT_TX_TYPEHASH = 0xeedfef42e81fe8cd0e4185e4320e9f8d52fd97eb890b85fa9bd7ad97c9a18de2;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::200 => uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::224 => require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::317 => require(uint256(s) >= uint256(1) * 65, "BSA021");
2023-01-biconomy-main/contracts/smart-contract-wallet/base/FallbackManager.sol::12 => bytes32 internal constant FALLBACK_HANDLER_STORAGE_SLOT = 0x6c9a6c4a39284e37ed1cf53d337577d14212a4870fb976a4366c693b939918d5;
2023-01-biconomy-main/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol::22 => let success := call(sub(gas(), 10000), token, 0, add(data, 0x20), mload(data), 0, 0x20)
2023-01-biconomy-main/contracts/smart-contract-wallet/common/Singleton.sol::9 => /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1 */
```





### [N04] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/BaseSmartAccount.sol::34 => using UserOperationLib for UserOperation;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::30 => using ECDSA for bytes32;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::31 => using LibAddress for address;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::30 => using ECDSA for bytes32;
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::31 => using LibAddress for address;
```



### [N05] NC-assembly method available

#### Impact
assembly{ id := chainid() } => uint256 id = block.chainid, assembly { size := extcodesize() } => uint256 size = address().code.length
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::144 => id := chainid()
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::144 => id := chainid()
```



### [N06] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/Proxy.sol::13 => event Received(uint indexed value, address indexed sender, bytes data);
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::64 => event ImplementationUpdated(address _scw, string version, address newImplementation);
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::65 => event EntryPointChanged(address oldEntryPoint, address newEntryPoint);
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::67 => event WalletHandlePayment(bytes32 txHash, uint256 payment);
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::64 => event ImplementationUpdated(address _scw, string version, address newImplementation);
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::65 => event EntryPointChanged(address oldEntryPoint, address newEntryPoint);
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::67 => event WalletHandlePayment(bytes32 txHash, uint256 payment);
2023-01-biconomy-main/contracts/smart-contract-wallet/base/Executor.sol::9 => event ExecutionFailure(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
2023-01-biconomy-main/contracts/smart-contract-wallet/base/Executor.sol::10 => event ExecutionSuccess(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
```




### [N07] Unused file


#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/Proxy.sol::1 => // SPDX-License-Identifier: MIT
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::1 => // SPDX-License-Identifier: MIT
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::1 => // SPDX-License-Identifier: MIT
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::1 => // SPDX-License-Identifier: MIT
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/Strings.sol::1 => // SPDX-License-Identifier: MIT
```




### [N08] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external .
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/BaseSmartAccount.sol::41 => function nonce() public view virtual returns (uint256);
2023-01-biconomy-main/contracts/smart-contract-wallet/BaseSmartAccount.sol::47 => function nonce(uint256 _batchId) public view virtual returns (uint256);
2023-01-biconomy-main/contracts/smart-contract-wallet/BaseSmartAccount.sol::53 => function entryPoint() public view virtual returns (IEntryPoint);
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::93 => function nonce() public view virtual override returns (uint256) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::97 => function nonce(uint256 _batchId) public view virtual override returns (uint256) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::101 => function entryPoint() public view virtual override returns (IEntryPoint) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::135 => function domainSeparator() public view returns (bytes32) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::140 => function getChainId() public view returns (uint256) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::166 => function init(address _owner, address _entryPointAddress, address _handler) public override initializer {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::197 => ) public payable virtual override returns (bool success) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::306 => ) public view virtual {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::401 => ) public view returns (bytes32) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::428 => ) public view returns (bytes memory) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::518 => function getDeposit() public view returns (uint256) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::525 => function addDeposit() public payable {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccount.sol::536 => function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::33 => function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address proxy){
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountFactory.sol::53 => function deployWallet(address _owner, address _entryPoint, address _handler) public returns(address proxy){
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::93 => function nonce() public view virtual override returns (uint256) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::97 => function nonce(uint256 _batchId) public view virtual override returns (uint256) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::101 => function entryPoint() public view virtual override returns (IEntryPoint) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::135 => function domainSeparator() public view returns (bytes32) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::140 => function getChainId() public view returns (uint256) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::166 => function init(address _owner, address _entryPointAddress, address _handler) public override initializer {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::197 => ) public payable virtual override returns (bool success) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::254 => ) public nonReentrant returns (uint256 payment) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::301 => ) public view virtual {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::391 => ) public view returns (bytes32) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::418 => ) public view returns (bytes memory) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::508 => function getDeposit() public view returns (uint256) {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::515 => function addDeposit() public payable {
2023-01-biconomy-main/contracts/smart-contract-wallet/SmartAccountNoAuth.sol::526 => function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
2023-01-biconomy-main/contracts/smart-contract-wallet/base/FallbackManager.sol::26 => function setFallbackHandler(address handler) public authorized {
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::32 => function enableModule(address module) public authorized {
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::47 => function disableModule(address prevModule, address module) public authorized {
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::66 => ) public virtual returns (bool success) {
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::85 => ) public returns (bool success, bytes memory returnData) {
2023-01-biconomy-main/contracts/smart-contract-wallet/base/ModuleManager.sol::105 => function isModuleEnabled(address module) public view returns (bool) {
2023-01-biconomy-main/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol::19 => function isValidSignature(bytes memory _data, bytes memory _signature) public view virtual returns (bytes4);
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/MultiSend.sol::26 => function multiSend(bytes memory transactions) public payable {
2023-01-biconomy-main/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol::21 => function multiSend(bytes memory transactions) public payable {
```



### [N09] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks
#### Findings:
```
2023-01-biconomy-main/contracts/smart-contract-wallet/common/SignatureDecoder.sol::31 => // use the second best option, 'and'
```






