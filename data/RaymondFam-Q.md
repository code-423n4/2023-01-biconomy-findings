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
Long bytes of literal values assigned to constants may incur typo/human error. In fact, it was validated in the constructor of Proxy.sol using `assert()` to compare the hashed constant with its `bytes32()` result to avoid this error:

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

## Open TODOs
Open TODOs can point to architecture or programming issues that still need to be resolved. Consider resolving them before deploying.

Here is an instance entailed:

[File: SmartAccountNoAuth.sol#L44](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L44)

```solidity
    // todo? rename wallet to account
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
[`max()` in Math.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L19-L21) should be imported and made used of instead of being re-implemented in the contract.

Here are some of the instances entailed:

[File: SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

```solidity
224:        require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");

181:    function max(uint256 a, uint256 b) internal pure returns (uint256) {
182:        return a >= b ? a : b;
183:    }
```
## Missing Checks for Contract Existence
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
