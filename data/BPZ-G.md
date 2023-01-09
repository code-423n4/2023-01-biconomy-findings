## ADD UNCHECKED {} FOR ITERATOR WHERE THE OPERANDS CANNOT OVERFLOW BECAUSE ITS ALLWAYS BELOW THE GIVEN NUMBER.
	
for loops j++ and i++ can be set to UNCHECKED{++j} and UNCHECKED{++i}

There are 7 instances of this issue:

        File: contracts/contract/MinipoolManager.sol

	100	for (uint256 i = 0; i < opasLen; i++) {
	
	107	for (uint256 a = 0; a < opasLen; a++) {
	
	112	for (uint256 i = 0; i < opslen; i++) {
	
	114	opIndex++;
	
	128	for (uint256 a = 0; a < opasLen; a++) {
	
	134	for (uint256 i = 0; i < opslen; i++) {
	
	136	 opIndex++;
	

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100

## FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE


if a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

There are 26 instances of this issue:

SmartAccountNoAuth.sol

	109	function setOwner(address _newOwner) external mixedAuth {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L109

	120	function updateImplementation(address _implementation) external mixedAuth {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L120

	127	function updateEntryPoint(address _newEntryPoint) external mixedAuth {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L127

	439	function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L439

	445	function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L445

	450	function execute(address dest, uint value, bytes calldata func) external onlyOwner{
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L450

	455	function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L455

	526	function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L526


SmartAccount.sol

	109	function setOwner(address _newOwner) external mixedAuth {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109

	120	function updateImplementation(address _implementation) external mixedAuth {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L120

	127	function updateEntryPoint(address _newEntryPoint) external mixedAuth {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L127

	449	function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449

	455	function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455

	460	function execute(address dest, uint value, bytes calldata func) external onlyOwner{
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460

	465	function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465

	536	function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L536

BasePaymaster.sol

	24	function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24

	67	function withdrawTo(address payable withdrawAddress, uint256 amount) public virtual onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L67

	75	function addStake(uint32 unstakeDelaySec) external payable onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L75

	90	function unlockStake() external onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90

	99	function withdrawStake(address payable withdrawAddress) external onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99


VerifyingSingletonPaymaster.sol

	65	function setSigner( address _newVerifyingSigner) external onlyOwner{ 
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65


BasePaymaster.sol

	24	function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L24

	67	function withdrawTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L67

	90	function unlockStake() external onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L90

	99	function withdrawStake(address payable withdrawAddress) external onlyOwner {
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L99
	

