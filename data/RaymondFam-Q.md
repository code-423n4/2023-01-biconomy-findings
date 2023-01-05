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
Long bytes of literal values assigned to constants may incur typo/human error. In fact, `assert()` was adopted in Proxy.sol constructor comparing the hashed constant with `bytes32()` to avoid this error:

[File: Proxy.sol#L16](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16)   

```solidity
         assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```
As such, consider at least assigning these constants with their corresponding expression where possible. For instance, the instance below could have its code line refactored as follows:

[File: SmartAccount.sol#L42-L48](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L42-L48)

```diff
-    bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH = 0x47e79534a245952e8b16893a336b85a3d9ea9fa8c573f3d803afb92a79469218;
+    bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH = keccak256("EIP712Domain(uint256 chainId,address verifyingContract)");
```
Or, better yet, use `assert()` to make it absolute error free in the constructor.  