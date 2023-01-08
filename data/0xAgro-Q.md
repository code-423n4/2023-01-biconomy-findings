# QA Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[NC-01]|Long Lines (> 120 Characters)|85|
|[NC-02]|Contracts Missing `@title` NatSpec Tag|25|
|[NC-03]|`TODO` Left In Production Code|11|
|[NC-04]|Order of Functions Not Compliant With Solidity Docs|8|
|[NC-05]|Contract Layout Voids Solidity Docs|7|
|[NC-06]|Commented Code Left In Production Code|6|
|[NC-07]|Order of Modifiers Not Compliant With Solidity Docs|5|
|[NC-08]|Debug Left in Production Code|5|
|[NC-09]|NatSpec Comments Use `//` Instead of `///`|3|
|[NC-10]|Dead Comments|2|
|[NC-11]|Inconsistent NatSpec Function Headers|2|
|[NC-12]|Underscore Notation Not Used / Not Used Consistently|1|
|[NC-13]|Power of Ten Literal > `10e3` Not In Scientific Notation|1|
|[NC-14]|Non-Assembly Function Available (`address().code.length`)|1|
|[NC-15]|Non-Assembly Function Available (`chainid()`)|1|
|[NC-16]|Whitespace In `mapping`|1|
|[NC-17]|`validate` Misspelled as `valiate`|1|
|[NC-18]|`// Emit events here..`|1|
|[NC-19]|Duplicate Comment|1|

### [NC-01] Long Lines (> 120 Characters)

Lines with greater length than 120 characters are used. The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

#### Findings:

*scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol*
 Links: [20](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L20), [24](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L24). 
```solidity
20:     *      In case there is a paymaster in the request (or the current deposit is high enough), this value will be zero.
24:    function validateUserOp(UserOperation calldata userOp, bytes32 userOpHash, address aggregator, uint256 missingAccountFunds)
```

*scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol*
 Links: [39](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L39), [45](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L45), [57](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L57), [60](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L60). 
```solidity
39:     * subclass should return a nonce value that is used both by _validateAndUpdateNonce, and by the external provider (to read the current nonce)
45:     * subclass should return a nonce value that is used both by _validateAndUpdateNonce, and by the external provider (to read the current nonce)
57:     * subclass doesn't need to override this method. Instead, it should override the specific internal validation methods.
60:    function validateUserOp(UserOperation calldata userOp, bytes32 userOpHash, address aggregator, uint256 missingAccountFunds)
```

*scw-contracts/contracts/smart-contract-wallet/Proxy.sol*
 Links: [10](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L10). 
```solidity
10:    /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1, and is validated in the constructor */
```

*scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol*
 Links: [42](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L42), [46](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L46), [186](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L186), [222](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L222), [223](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L223), [227](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L227), [228](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L228), [229](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L229), [230](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L230), [231](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L231), [233](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L233), [239](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L239), [300](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L300), [320](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L320), [327](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L327), [339](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L339), [342](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L342), [346](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L346), [356](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L356), [357](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L357), [489](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489). 
```solidity
42:    bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH = 0x47e79534a245952e8b16893a336b85a3d9ea9fa8c573f3d803afb92a79469218;
46:    //     "AccountTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"
186:    /// @dev Allows to execute a Safe transaction confirmed by required number of owners and then pays the account that submitted the transaction.
222:        // We require some gas to emit the events (at least 2500) after the execution and some to perform code until the execution (500)
223:        // We also include the 1/64 in the check that is not send along with a call to counteract potential shortings because of EIP-150
227:            // If the gasPrice is 0 we assume that nearly all available gas can be used (it is always more than targetTxGas)
228:            // We only substract 2500 (compared to the 3000 before) to ensure that the amount passed is still higher than targetTxGas
229:            success = execute(_tx.to, _tx.value, _tx.data, _tx.operation, refundInfo.gasPrice == 0 ? (gasleft() - 2500) : _tx.targetTxGas);
230:            // If no targetTxGas and no gasPrice was set (e.g. both are 0), then the internal tx is required to be successful
231:            // This makes it possible to use `estimateGas` without issues, as it searches for the minimum gas where the tx doesn't revert
233:            // We transfer the calculated tx costs to the tx.origin to avoid sending it to intermediate contracts that have made calls
239:                payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);
300:     * @param signatures Signature data that should be verified. Can be ECDSA signature, contract signature (EIP-1271) or approved hash.
320:                // This check is not completely accurate, since it is possible that more signatures than the threshold are send.
327:                // Check if the contract signature is in bounds: start of data is s + 32 and end is start + signature length
339:                    // The signature data for contract signatures is appended to the concatenated signatures and the offset is stored in s
342:                require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");
346:            // To support eth_sign and similar we adjust v and hash the messageHash with the Ethereum message prefix before applying ecrecover
356:    ///      This method is only meant for estimation purpose, therefore the call will always revert and encode the result in the revert data.
357:    ///      Since the `estimateGas` function includes refunds, call this method to get an estimated of the costs that are deducted from the safe with `execTransaction`
489:    function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        
```

*scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol*
 Links: [7](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L7). 
```solidity
7:    // singleton slot always needs to be first declared variable, to ensure that it is at the same location as in the Proxy contract.
```

*scw-contracts/contracts/smart-contract-wallet/interfaces/IERC165.sol*
 Links: [4](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC165.sol#L4). 
```solidity
4:/// @notice More details at https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/introspection/IERC165.sol
```

*scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol*
 Links: [114](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L114). 
```solidity
114:    function getModulesPaginated(address start, uint256 pageSize) external view returns (address[] memory array, address next) {
```

*scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol*
 Links: [12](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L12). 
```solidity
12:    bytes32 internal constant FALLBACK_HANDLER_STORAGE_SLOT = 0x6c9a6c4a39284e37ed1cf53d337577d14212a4870fb976a4366c693b939918d5;
```

*scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol*
 Links: [8](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol#L8). 
```solidity
8:    /// @param pos which signature to read. A prior bounds check of this parameter should be performed, to avoid out of bounds access
```

*scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol*
 Links: [24](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L24), [33](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L33). 
```solidity
24:    event SmartAccountCreated(address indexed _proxy, address indexed _implementation, address indexed _owner, string version, uint256 _index);
33:    function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address proxy){
```

*scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol*
 Links: [10](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L10), [11](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L11), [13](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L13), [31](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L31), [32](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L32), [34](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L34), [37](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L37), [38](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L38). 
```solidity
10:        @dev An ERC1155-compliant smart contract MUST call this function on the token recipient contract, at the end of a `safeTransferFrom` after the balance has been updated.        
11:        This function MUST return `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))` (i.e. 0xf23a6e61) if it accepts the transfer.
13:        Return of any other value than the prescribed keccak256 generated value MUST result in the transaction being reverted by the caller.
31:        @dev An ERC1155-compliant smart contract MUST call this function on the token recipient contract, at the end of a `safeBatchTransferFrom` after the balances have been updated.        
32:        This function MUST return `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))` (i.e. 0xbc197c81) if it accepts the transfer(s).
34:        Return of any other value than the prescribed keccak256 generated value MUST result in the transaction being reverted by the caller.
37:        @param _ids       An array containing ids of each token being transferred (order and length must match _values array)
38:        @param _values    An array containing amounts of each token being transferred (order and length must match _ids array)
```

*scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol*
 Links: [10](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol#L10), [26](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol#L26). 
```solidity
10:   *   > The bytes4 magic value to return when signature is valid is 0x20c13b0b : bytes4(keccak256("isValidSignature(bytes,bytes)")
26:   *   > The bytes4 magic value to return when signature is valid is 0x20c13b0b : bytes4(keccak256("isValidSignature(bytes,bytes)")
```

*scw-contracts/contracts/smart-contract-wallet/libs/Math.sol*
 Links: [51](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L51), [95](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L95), [119](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L119). 
```solidity
51:     * @notice Calculates floor(x * y / denominator) with full precision. Throws if result overflows a uint256 or denominator == 0
95:            // Factor powers of two out of denominator and compute largest power of two divisor of denominator. Always >= 1.
119:            // Use the Newton-Raphson iteration to improve the precision. Thanks to Hensel's lifting lemma, this also works
```

*scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol*
 Links: [48](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L48), [90](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L90), [168](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L168), [187](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L187), [223](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L223), [242](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L242), [289](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L289), [319](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L319), [349](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L349), [363](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L363), [385](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L385), [401](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L401), [409](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L409), [440](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L440), [458](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L458), [476](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L476). 
```solidity
48:    function _executeUserOp(uint256 opIndex, UserOperation calldata userOp, UserOpInfo memory opInfo) private returns (uint256 collected) {
90:     * @param opsPerAggregator the operations to execute, grouped by aggregator (or address(0) for no-aggregator accounts)
168:    function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost) {
187:        //note: opIndex is ignored (relevant only if mode==postOpReverted, which is only possible outside of innerHandleOp)
223:     * @dev The node must also verify it doesn't use banned opcodes, and that it doesn't reference storage outside the account's data.
242:            revert SimulationResultWithAggregation(preOpGas, prefund, deadline, senderInfo, factoryInfo, paymasterInfo, aggregatorInfo);
289:    function _validateAccountPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, address aggregator, uint256 requiredPrefund)
319:        try IAccount(sender).validateUserOp{gas : mUserOp.verificationGasLimit}(op, opInfo.userOpHash, aggregator, missingAccountFunds) returns (uint256 _deadline) {
349:    function _validatePaymasterPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, uint256 requiredPreFund, uint256 gasUsedByValidateAccountPrepayment) internal returns (bytes memory context, uint256 deadline) {
363:        try IPaymaster(paymaster).validatePaymasterUserOp{gas : gas}(op, opInfo.userOpHash, requiredPreFund) returns (bytes memory _context, uint256 _deadline){
385:    function _validatePrepayment(uint256 opIndex, UserOperation calldata userOp, UserOpInfo memory outOpInfo, address aggregator)
401:        (gasUsedByValidateAccountPrepayment, actualAggregator, deadline) = _validateAccountPrepayment(opIndex, userOp, outOpInfo, aggregator, requiredPreFund);
409:            (context, paymasterDeadline) = _validatePaymasterPrepayment(opIndex, userOp, outOpInfo, requiredPreFund, gasUsedByValidateAccountPrepayment);
440:    function _handlePostOp(uint256 opIndex, IPaymaster.PostOpMode mode, UserOpInfo memory opInfo, bytes memory context, uint256 actualGas) private returns (uint256 actualGasCost) {
458:                    try IPaymaster(paymaster).postOp{gas : mUserOp.verificationGasLimit}(mode, context, actualGasCost) {}
476:        emit UserOperationEvent(opInfo.userOpHash, mUserOp.sender, mUserOp.paymaster, mUserOp.nonce, success, actualGasCost, actualGas);
```

*scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol*
 Links: [45](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L45). 
```solidity
45:        postOpReverted //user op succeeded, but caused postOp to revert. Now its a 2nd call, after user's op was deliberately reverted.
```

*scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol*
 Links: [9](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L9). 
```solidity
9: * - the validateUserOp will be called only after the aggregator validated this account (with all other accounts of this aggregator).
```

*scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol*
 Links: [25](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L25), [28](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L28), [46](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L46), [57](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L57), [92](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L92), [109](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L109). 
```solidity
25:     * @param actualGasUsed - total gas used by this UserOperation (including preVerification, creation, validation and execution)
28:    event UserOperationEvent(bytes32 indexed userOpHash, address indexed sender, address indexed paymaster, uint256 nonce, bool success, uint256 actualGasCost, uint256 actualGasUsed);
46:    event UserOperationRevertReason(bytes32 indexed userOpHash, address indexed sender, uint256 nonce, bytes revertReason);
57:     *  @param paymaster - if paymaster.validatePaymasterUserOp fails, this will be the paymaster's address. if validateUserOp failed,
92:     * @param opsPerAggregator the operations to execute, grouped by aggregator (or address(0) for no-aggregator accounts)
109:     * @dev The node must also verify it doesn't use banned opcodes, and that it doesn't reference storage outside the account's data.
```

*scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol*
 Links: [13](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L13), [16](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L16). 
```solidity
13:     * @param preVerificationGas gas not calculated by the handleOps method, but added to the gas paid. Covers batch overhead.
16:     * @param paymasterAndData if set, this field hold the paymaster address and "paymaster-specific-data". the paymaster will pay for the transaction instead of the sender
```

*scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol*
 Links: [35](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L35). 
```solidity
35:    function aggregateSignatures(UserOperation[] calldata userOps) external view returns (bytes memory aggregatesSignature);
```

*scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol*
 Links: [46](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L46), [106](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L106), [108](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L108), [109](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L109). 
```solidity
46:     * add a deposit for this paymaster and given paymasterId (Dapp Depositor address), used for paying for transaction fees
106:        // we only "require" it here so that the revert reason on invalid signature will be of "VerifyingPaymaster", and not "ECDSA"
108:        require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");
109:        require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");
```

*scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol*
 Links: [38](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L38), [42](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L42), [48](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L48). 
```solidity
38:                // We shift by 248 bits (256 - 8 [operation byte]) it right since mload will always load 32 bytes (a word).
42:                // We shift it right by 96 bits (256 - 160 [20 address bytes]) to right-align the data and zero out unused data.
48:                // We offset the load address by 85 byte (operation byte + 20 address bytes + 32 value bytes + 32 data length bytes)
```

*scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol*
 Links: [32](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L32), [36](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L36), [42](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L42). 
```solidity
32:                // We shift by 248 bits (256 - 8 [operation byte]) it right since mload will always load 32 bytes (a word).
36:                // We shift it right by 96 bits (256 - 160 [20 address bytes]) to right-align the data and zero out unused data.
42:                // We offset the load address by 85 byte (operation byte + 20 address bytes + 32 value bytes + 32 data length bytes)
```

### [NC-02] Contracts Missing `@title` NatSpec Tag

25 out of 36 of the contracts in scope are missing a `@title` tag. Given that 11 contracts all have a `@title` tag, consider adding one per the 25 remaining contracts.

#### Findings:

[IAccount.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol), [BaseSmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol), [SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol), [IERC165.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC165.sol), [ISignatureValidator.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol), [SmartAccountFactory.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol), [ERC1155TokenReceiver.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol), [ERC721TokenReceiver.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol), [ERC777TokensRecipient.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol), [IERC1271Wallet.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol), [LibAddress.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol), [Math.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol), [EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol), [SenderCreator.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol), [StakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol), [IPaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol), [IAggregatedAccount.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol), [IEntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol), [Exec.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol), [IStakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol), [UserOperation.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol), [IAggregator.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol), [BasePaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol), [PaymasterHelpers.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol), and [VerifyingSingletonPaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol) are missing a `@title` tag.

### [NC-03] `TODO` Left In Production Code

`TODO`s should not be in production code. Each `TODO` should either be discarded or implemented if needed prior to production.

#### Findings:

*scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol*
 Links: [255](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255). 
```solidity
255:        // TODO: copy logic of gasPrice?
```

*/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol*
Links: [59](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L59), [86](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L86), 
```solidity
59:	// review virtual 
86:	// Review if we need to make view function
```

*/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol*
Links: [44](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L44), [58](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L58), [313](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L313).
```solidity
44:	// review? if rename wallet to account is must
58:	// review
313:	//review
```

*/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol*
Links: [16](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L16).
```solidity
16:	// Review for sig collision and HAL-04 report i
```

*/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol*
Links: [14](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L14).
```solidity
14:	// review need and impact of this update wallet -> account
```

*/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol*
Links: [8](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L8), [12](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L12).
```solidity
8:	// Could add a flag fromEntryPoint for AA txn
12:	// Could add a flag fromEntryPoint for AA txn
```

*/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol*
Links: [15](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L15).
```solidity
15:	//@review
```

### [NC-04] Order of Functions Not Compliant With Solidity Docs

The Solidity Style Guide suggests the following function ordering: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

#### Findings:

The following contracts are not compliant (examples are only to prove the functions are out of order NOT a full description): 

[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol): external functions are positioned after public functions.
[ModuleManager.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol): public functions are positioned after internal functions.
[FallbackManager.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol): public functions are positioned after internal functions.
[SmartAccountFactory.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol): external functions are positioned after public functions.
[EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol): public functions are positioned after private functions.
[StakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol): public functions are positioned after internal functions.
[BasePaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol): external functions are positioned after public functions.
[VerifyingSingletonPaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol): external functions are positioned after public functions.

### [NC-05] Contract Layout Voids Solidity Docs

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) suggests the following contract layout order: Type declarations, State variables, Events, Modifiers, Functions.

#### Findings:

The following contracts are not compliant (examples are only to prove the layout are out of order NOT a full description): 

[ModuleManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol): State is positioned after Events.
[FallbackManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol): State is positioned after Events.
[SmartAccountFactory.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol): Events are positioned after Functions.
[EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol): Structs are positioned after Functions.
[IPaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol): Enums are positioned after Functions.
[IEntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol): Structs are positioned after Events
[IStakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol): Structs are positioned after Events.

### [NC-06] Commented Code Left In Production Code

Commented code should be removed or implemented prior to production. Consider removing all commented code.

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol*
Links: [10](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L10), [25](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L25), [125](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L125).
```solidity
10:	// import "../samples/Signatures.sol";
25:	// possibly //  using Signatures for UserOperation;
125:	// (mode,context,actualGasCost); // unused params
```

*/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol*
Links: [53](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L53), [255](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L255), [267](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L267).
```solidity
53:	// uint96 private _nonce; //changed to 2D nonce below
255:	// uint256 startGas = gasleft();
267:	// uint256 requiredGas = startGas - gasleft();
```

### [NC-07] Order of Modifiers Not Compliant With Solidity Docs

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#function-declaration) suggests the following modifer order:  Visibility, Mutability, Virtual, Override, Custom modifiers.

#### Findings:

*scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol* ([R](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol))

* ` validateUserOp` ('external', 'override', 'virtual'): virtual (Virtual) positioned after override (Override).
* ` _requireFromEntryPoint` ('internal', 'virtual', 'view'): view (Mutability) positioned after virtual (Virtual).

*scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol* ([R](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol))

* ` handleAggregatedOps` ('payable', 'public'): public (Visibility) positioned after payable (Mutability).

*scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol* ([R](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol))

* ` deposit` ('public', 'virtual', 'payable'): payable (Mutability) positioned after virtual (Virtual).

*scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol* ([R](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol))

* ` deposit` ('public', 'virtual', 'override', 'payable'): payable (Mutability) positioned after override (Override).

### [NC-08] Debug Left in Production Code

Commented debug lines should be taken out before production (EX. console.log).

#### Findings:

*scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol*
 Links: [201](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L201), [237](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L237), [243](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L243), [268](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L268), [292](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L292). 
```solidity
201:        //console.log("init %s", 21000 + msg.data.length * 8);
237:                //console.log("sent %s", startGas - gasleft());
243:            //console.log("extra gas %s ", extraGas);
268:        //console.log("hp %s", requiredGas);
292:        //console.log("hpr %s", requiredGas);
```

### [NC-09] NatSpec Comments Use `//` Instead of `///`

The [NatSpec notation](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html#documentation-example) requires the use of `///` or `/**` to differentiate from regular comments. There are instances in the codebase where a `//` is used instead of `///`.

#### Findings:

*scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol*
 Links: [54](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L54), [105](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L105), [499](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L499). 
```solidity
54:    // @notice there is no _nonce 
105:    // @notice authorized modifier (onlySelf) is already inherited
499:    // @notice Nonce space is locked to 0 for AA transactions
```

### [NC-10] Dead Comments

On [L85](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L85) of [EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol) an `unchecked` brace is commented indicating it is an `unchecked` brace. Seeing the `unchecked` brace is enough. [L291](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L291) also has an `unchecked` brace with no comment (non-consistant). Consider removing dead comments.

On [L187](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L187) of [EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol) `note: ` is used. All comments can be viewed as a note, consider removing `note: `.

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol*
Links: [85](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L85), [187](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L187).
```solidity
85:	} //unchecked
187:	//note: opIndex is ignored (relevant only if mode==postOpReverted, which is only possible outside of innerHandleOp)
```

### [NC-11] Inconsistent NatSpec Function Headers

In [IStakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol) the `balanceOf` function has a single line NatSpec header where the `depositTo` function does aswell; however, one uses `///`, one uses `/**`. Both `///` and `/**` are valid NatSpec notation; however, it is best to stick to one style for readability.

The same can be said for `_requireFromEntryPoint` in [BasePaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol) and `getDeposit` respectively.

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol*
Links: [69](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L69).
```solidity
69:	/// return the deposit (for gas payment) of the account
```

*/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol*
Links: [103](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L103).
```solidity
103:	/// validate the call is made from a valid entrypoint
```

### [NC-12] Underscore Notation Not Used / Not Used Consistently

Consider using underscore notation to help with contract readability (Ex. `23453` -> `23_453`).

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol*
Links: [200](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L200).
```solidity
200:	uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
```

### [NC-13] Power of Ten Literal > `10e3` Not In Scientific Notation

Power of ten literals > `10e3` are easier to read when expressed in scientific notation. Consider expressing large powers of ten in scientific notation (Ex. 10e5).

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol*
Links: [22](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L22).
```solidity
22:	let success := call(sub(gas(), 10000), token, 0, add(data, 0x20), mload(data), 0, 0x20)
```

### [NC-14] Non-Assembly Function Available (`address().code.length`)

The complexity brought by assembly can be avoided by using built in functions when there is no need for assembly. `assembly { csize := extcodesize(account) }` can be replaced by `uint256 csize = address().code.length`.

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol*
Links: [14](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol#L14).
```solidity
14:	assembly { csize := extcodesize(account) }
```

### [NC-15] Non-Assembly Function Available (`chainid()`)

The complexity brought by assembly can be avoided by using built in functions when there is no need for assembly. `assembly { id := chainid() }` can be replaced by `uint256 id = block.chainid`.

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol*
Links: [144](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L144).
```solidity
144:	id := chainid()
```

### [NC-16] Whitespace In `mapping`

Mapping whitespace not in accordance with the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#mappings).

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol*
Links: [15](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L15).
```solidity
15:	mapping (address => bool) public isAccountExist;
```
**Suggested Change**
```solidity
15:	mapping(address => bool) public isAccountExist;
```

### [NC-17] `validate` Misspelled as `valiate`

`validate` is misspelled as `valiate`. Consider fixing the spelling mistake.

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol*
Links: [10](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L10).
```solidity
10:	 * - the validateUserOp MUST valiate the aggregator parameter, and MAY ignore the userOp.signature field.
```

### [NC-18] `// Emit events here..`

There is a comment that seems to have been left in while creating the `execute` function in [Executor.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol) that reads: `// Emit events here..`. If `// Emit events here..` was meant to be deleted prior to production remove it. At the very least there is an extra `.` that should be removed regardless.

#### Findings:

*/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol*
Links: [31](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L31).
```solidity
31:	// Emit events here..
```

### [NC-19] Duplicate Comment

Duplicate comments should be removed.

### Findings:

*/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol*
Links: [8](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L8) / [12](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L12).
```solidity
8:	// Could add a flag fromEntryPoint for AA txn
12:	// Could add a flag fromEntryPoint for AA txn
```
