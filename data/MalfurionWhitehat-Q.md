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

# 2. Do not use `tx.origin` for testing purposes on `SmartAccount._validateSignature`. Instead, create a mock implementation that overrides this method and bypasses any necessary signature validation.

Mixing testing and deployment code will unnecessarily introduce dead code on production contracts and will make hinder maintainability.

```diff
diff --git a/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol b/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
index c4f69a8..78e960b 100644
--- a/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
+++ b/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
@@ -508,7 +508,7 @@ contract SmartAccount is
         bytes32 hash = userOpHash.toEthSignedMessageHash();
         //ignore signature mismatch of from==ZERO_ADDRESS (for eth_callUserOp validation purposes)
         // solhint-disable-next-line avoid-tx-origin
-        require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
+        require(owner == hash.recover(userOp.signature), "account: wrong signature");
         return 0;
     }
 
``` 

# 3. Emit events on important state variable changing operations: `VerifyingSingletonPaymaster.setSigner`

```diff
diff --git a/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol b/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
index 7716a01..6534bda 100644
--- a/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
+++ b/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
@@ -28,6 +28,8 @@ contract VerifyingSingletonPaymaster is BasePaymaster {
     using PaymasterHelpers for bytes;
     using PaymasterHelpers for PaymasterData;
 
+    event VerifyingSignerChanged(address indexed oldVerifyingSigner, address indexed newVerifyingSigner);
+
     mapping(address => uint256) public paymasterIdBalances;
 
     address public verifyingSigner;
@@ -64,6 +66,7 @@ contract VerifyingSingletonPaymaster is BasePaymaster {
     */
     function setSigner( address _newVerifyingSigner) external onlyOwner{
         require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
+        emit VerifyingSignerChanged(verifyingSigner, _newVerifyingSigner);
         verifyingSigner = _newVerifyingSigner;
     }
 

```