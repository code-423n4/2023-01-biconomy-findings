# 1. Use `block.chainid` on `SmartAccount.getChainId`

The use of inline assembly is unnecessary here:

```diff
diff --git a/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol b/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
index c4f69a8..928c16a 100644
--- a/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
+++ b/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
@@ -138,12 +138,7 @@ contract SmartAccount is
 
     /// @dev Returns the chain id used by this contract.
     function getChainId() public view returns (uint256) {
-        uint256 id;
-        // solhint-disable-next-line no-inline-assembly
-        assembly {
-            id := chainid()
-        }
-        return id;
+        return block.chainid;
     }
 
     //@review getNonce specific to EntryPoint requirements

```