## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach.

For instance, the import instances below could be refactored as follows:

[File: ModuleManager.sol#L4-L6](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L4-L6)

```diff
- import "../common/Enum.sol";
- import "../common/SelfAuthorized.sol";
- import "./Executor.sol";
+ import {Enum} from "../common/Enum.sol";
+ import {SelfAuthorized} from "../common/SelfAuthorized.sol";
+ import {Executor} from "./Executor.sol";
```
## Expression for Hashed Values
Long bytes of literal values assigned to constants may incur typo/human error. In fact, it was validated in the constructor of Proxy.sol and Singleton.sol using `assert()` to compare the hashed constant with its `bytes32()` result to avoid this error:

[File: Singleton.sol#L13](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13)
[File: Proxy.sol#L16](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16)   

```solidity
         assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```
As such, consider at least assigning these constants with their corresponding `bytes32()` expression where possible. For instance, the instance below could have its code line refactored as follows:

[File: SmartAccount.sol#L42-L48](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L42-L48)

```diff
-    bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH = 0x47e79534a245952e8b16893a336b85a3d9ea9fa8c573f3d803afb92a79469218;
+    bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH = keccak256("EIP712Domain(uint256 chainId,address verifyingContract)");
```
Or, better yet, use `assert()` to make it absolute error free in the constructor.  

## Missing `tokenGasPriceFactor`
In `encodeTransactionData()` of SmartAccount.sol, `tokenGasPriceFactor` is missing in `keccak256(abi.encode())`. Consider commenting the omission adopted for this particular variable where possible for more distinct code readability.

## Open TODOs
Open TODOs can point to architecture or programming issues that still need to be resolved. Consider resolving them before deploying.

Here is the one instance entailed:

[File: EntryPoint.sol#L255](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255)

```solidity
        // TODO: copy logic of gasPrice?
```
## Empty blocks
Function with empty block should have a comment explaining why it is empty, or an event emitted.

For instance, the fallback instance may be refactored as follows just as it has been done in Proxy.sol:

[File: SmartAccount.sol#L550](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550)

```diff
+ event Received(uint indexed value, address indexed sender, bytes data);

-    receive() external payable {}
+    receive() external payable {
+        emit Received(msg.value, msg.sender, "");
+    }
```
## Library not adequately utilized
Library functions in [Math.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L19-L21) should be imported and fully made used of in the protocol instead of being re-implemented in the contracts.

Here are the `max()` and `min()` instances entailed:

[File: SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

```solidity
224:        require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");

181:    function max(uint256 a, uint256 b) internal pure returns (uint256) {
182:        return a >= b ? a : b;
183:    }
```
[File: UserOperation.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol)

```solidity
53:        return min(maxFeePerGas, maxPriorityFeePerGas + block.basefee);

77:    function min(uint256 a, uint256 b) internal pure returns (uint256) {
78:        return a < b ? a : b;
79:    }
```
## Missing checks for contract existence
Performing a low-level calls without confirming contract’s existence (not yet deployed or have been destructed which could still be a non-zero address) could return success even though no function call was executed as documented in the link below:

https://docs.soliditylang.org/en/v0.8.7/control-structures.html#error-handling-assert-require-revert-and-exceptions

Consider check for target contract existence before call.

Here is an instance entailed that could be refactored by making use of [LibAddress.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol) that has already been imported to the contract:

[File: SmartAccount.sol#L449-L453](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449-L453)

```diff
    function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
        require(dest != address(0), "this action will burn your funds");
+        require(dest.isContract(), "INVALID_IMPLEMENTATION");
        (bool success,) = dest.call{value:amount}("");
        require(success,"transfer failed");
    }
```
## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here is a contract instance with missing NatSpec in its entirety:

[File: ERC777TokensRecipient.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol)

`checkSignatures()` missing `@param data`:

[File: SmartAccount.sol#L297-L306](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L297-L306)

## Commented codes
Throughout the code base, there are several commented code lines that could point to items that are not done or need redesigning, be a mistake, debugging or just be testing overhead. Consider removing them before deployment for readability and conciseness.

Here are some of the instances entailed:

[File: SmartAccount.sol#L68-L69](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L68-L69)

```solidity
    // nice to have
    // event SmartAccountInitialized(IEntryPoint indexed entryPoint, address indexed owner);
```
[File: SmartAccount.sol#L201](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L201)

```solidity
        //console.log("init %s", 21000 + msg.data.length * 8);
```
[File: SmartAccountFactory.sol#L22](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L22)

```solidity
    // event SmartAccountCreated(address indexed _proxy, address indexed _implementation, address indexed _owner);
```
## Use `delete` to clear variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address or a boolean to false. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic.

For instance, the `a[x]` instance below may be refactored as follows:
  
[File: ModuleManager.sol#L52](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L52)

```diff
-        modules[module] = address(0);
+        delete modules[module];
```
## `uint256` over `uint`
Across the code base, there are numerous instances of `uint`, as opposed to `uint256`. In favor of explicitness, consider replacing all instances of `uint` with `uint256`.

Here are some of the instances entailed:

[File: SmartAccountFactory.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol)

```
35:        bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));

54:        bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));

69:       bytes memory code = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
```
## Inconsistency in interface naming
Some interfaces in the code bases are named without the prefix `I` that could cause confusion to developers and readers referencing or interacting with the protocol. Consider conforming to Solidity's naming conventions by having the instances below refactored as follow:

[File: ERC1155TokenReceiver.sol#L7](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L7)

```diff
- interface ERC1155TokenReceiver {
+ interface IERC1155TokenReceiver {
```
[File: ERC721TokenReceiver.sol#L5](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol#L5)

```diff
- interface ERC721TokenReceiver {
+ interface IERC721TokenReceiver {
```
[File: ERC777TokensRecipient.sol#L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol#L4)

```diff
- interface ERC777TokensRecipient {
+ interface IERC777TokensRecipient {
```
## Non-compliant contract layout with Solidity's Style Guide
According to Solidity's Style Guide below:

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

In order to help readers identify which functions they can call, and find the constructor and fallback definitions more easily, functions should be grouped according to their visibility and ordered in the following manner:

constructor, receive function (if exists), fallback function (if exists), external, public, internal, private

And, within a grouping, place the `view` and `pure` functions last.

Additionally, inside each contract, library or interface, use the following order:

type declarations, state variables, events, modifiers, functions

Consider adhering to the above guidelines for all contract instances entailed.

## Contracts should be imported
In `ISignatureValidator.sol`, the contract `ISignatureValidatorConstants` is showing up in its entirety at the top of the abstract contract `ISignatureValidator` which facilitates ease of references on the same file page. 

Additionally, both the contract and the abstract contract have been named with the prefix `I` that is generally (if not exclusively) reserved for interfaces.

Consider:
1. having their names renamed as follows:

[File: ISignatureValidator.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol)

```solidity
// SPDX-License-Identifier: LGPL-3.0-only
pragma solidity 0.8.12;

- contract ISignatureValidatorConstants {
+ contract SignatureValidatorConstants {
    // bytes4(keccak256("isValidSignature(bytes,bytes)")
    bytes4 internal constant EIP1271_MAGIC_VALUE = 0x20c13b0b;
}

- abstract contract ISignatureValidator is ISignatureValidatorConstants {
+ abstract contract SignatureValidator is ISignatureValidatorConstants {
    /**
     * @dev Should return whether the signature provided is valid for the provided data
     * @param _data Arbitrary length data signed on the behalf of address(this)
     * @param _signature Signature byte array associated with _data
     *
     * MUST return the bytes4 magic value 0x20c13b0b when function passes.
     * MUST NOT modify state (using STATICCALL for solc < 0.5, view modifier for solc > 0.5)
     * MUST allow external calls
     */
    function isValidSignature(bytes memory _data, bytes memory _signature) public view virtual returns (bytes4);
}
```
2. saving the contract entailed into SignatureValidatorConstants.sol, and have it imported like it has been done in all other contracts or abstract contracts.

## Typo mistakes
[File: ISignatureValidator.sol#L12](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol#L12)

```diff
-     * @param _data Arbitrary length data signed on the behalf of address(this)
+     * @param _data Arbitrary length data signed on behalf of address(this)
```
[File: SmartAccount.sol#L44](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L44)

```diff
-    // review? if rename wallet to account is must
+    // review? if renaming wallet to account is a must
```
[File: SmartAccount.sol#L74](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L74)

```diff
-     * @notice Throws if the sender is not an the owner.
+     * @notice Throws if the sender is not the owner.
```
[File: SmartAccount.sol#L153](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L153)

```diff
-     * @return nonce : the number of transaction made within said batch
+     * @return nonce : the number of transactions made within said batch
```
[File: SignatureDecoder.sol#L30-L31](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol#L30-L31)

```diff
-            // 'byte' is not working due to the Solidity parser, so lets
+            // 'byte' is not working due to the Solidity parser, so let's
            // use the second best option, 'and'
```
[File: SmartAccount.sol#L320](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L320)

```diff
-                // This check is not completely accurate, since it is possible that more signatures than the threshold are send.
+                // This check is not completely accurate, since it is possible that more signatures than the threshold are sent.
```
[File: SmartAccount.sol#L382](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L382)

```diff
-    /// @param targetTxGas Fas that should be used for the safe transaction.
+    /// @param targetTxGas Gas that should be used for the safe transaction.
```
[File: SignatureDecoder.sol#L4](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol#L4)

```diff
- /// @title SignatureDecoder - Decodes signatures that a encoded as bytes
+ /// @title SignatureDecoder - Decodes signatures that are encoded as bytes
```
[File: IERC1271Wallet.sol#L12](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol#L12)

```diff
-   * @param _data       Arbitrary length data signed on the behalf of address(this)
+   * @param _data       Arbitrary length data signed on behalf of address(this)
```
## `address(this)` over `this`
As denoted in [Solidity Documentation](https://docs.soliditylang.org/en/v0.5.0/units-and-global-variables.html):

"Prior to version 0.5.0, Solidity allowed address members to be accessed by a contract instance, for example this.balance. This is now forbidden and an explicit conversion to address must be done: address(this).balance."

Here is an instance entailed:

[File: SmartAccount.sol#L136](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L136)

```diff
-        return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
+        return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), address(this)));
```
## Lack of events for critical operations
Critical operations not triggering events will make it difficult to review the correct behavior of the deployed contracts. Users and blockchain monitoring systems will not be able to detect suspicious behaviors at ease without events. Consider adding events where appropriate for all critical operations for better support of off-chain logging API.

Here is an instance entailed:

[File: VerifyingSingletonPaymaster.sol#L65-L68](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65-L68)

```solidity
    function setSigner( address _newVerifyingSigner) external onlyOwner{
        require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
        verifyingSigner = _newVerifyingSigner;
    }
```