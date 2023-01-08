# 1. Remove duplicate validation of address zero in `SmartAccount.init`

```diff
diff --git a/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol b/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
index c4f69a8..cc30425 100644
--- a/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
+++ b/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
@@ -168,10 +168,10 @@ contract SmartAccount is
         require(address(_entryPoint) == address(0), "Already initialized");
         require(_owner != address(0),"Invalid owner");
         require(_entryPointAddress != address(0), "Invalid Entrypoint");
-        require(_handler != address(0), "Invalid Entrypoint");
+        require(_handler != address(0), "Invalid Fallback Handler");
         owner = _owner;
         _entryPoint =  IEntryPoint(payable(_entryPointAddress));
-        if (_handler != address(0)) internalSetFallbackHandler(_handler);
+        internalSetFallbackHandler(_handler);
         setupModules(address(0), bytes(""));
     }
 

```

# 2. Remove unnecessary owner validation on `SmartAccount.execute` and `SmartAccount.executeBatch` as these functions are already behind an `onlyOwner` modifier

```diff
diff --git a/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol b/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
index c4f69a8..67a2d5f 100644
--- a/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
+++ b/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
@@ -458,12 +458,10 @@ contract SmartAccount is
     }
 
     function execute(address dest, uint value, bytes calldata func) external onlyOwner{
-        _requireFromEntryPointOrOwner();
         _call(dest, value, func);
     }
 
     function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
-        _requireFromEntryPointOrOwner();
         require(dest.length == func.length, "wrong array lengths");
         for (uint i = 0; i < dest.length;) {
             _call(dest[i], 0, func[i]);

```