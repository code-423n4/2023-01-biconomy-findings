## Private function with embedded modifier reduces contract size
Consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A private visibility that saves more gas on function calls than the internal visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows:

[File: SmartAccountNoAuth.sol#L82-L85](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L82-L85)

```diff
+    function _mixedAuth() private view {
+        require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
+    }

   modifier mixedAuth {
-        require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
+        _mixedAuth();
       _;
   }
```
## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: SmartAccountNoAuth.sol#L155-L159](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L155-L159)

```diff
    function getNonce(uint256 batchId)
    public view
-    returns (uint256) {
+    returns (uint256 _2dNonce) {
-        return nonces[batchId];
+        _2dNonce = nonces[batchId];
    }
```