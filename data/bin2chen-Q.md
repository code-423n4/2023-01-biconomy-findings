1.
SmartAccount._validateSignature()
According to protocol eip-4337, the _validateSignature() signature error should return "sigFailure", not the current direct revert.
```solidity
    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)
    internal override virtual returns (uint256 deadline) {
...    
require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
...
```

https://eips.ethereum.org/EIPS/eip-4337
"""
The return value is packed of sigFailure, validUntil and validAfter timestamps.
sigFailure is 1 byte value of “1” the signature check failed (should not revert on signature failure, to support estimate)
validUntil is 8-byte timestamp value, or zero for “infinite”. The UserOp is valid only up to this time.
validAfter is 8-byte timestamp. The UserOp is valid only after this time.
"""

2.
SmartAccount.execute()/executeBatch（）has been limited to onlyOwner, no need to verify _requireFromEntryPointOrOwner();
SmartAccount.sol suggest remove onlyOwner:
```solidity
-   function execute(address dest, uint value, bytes calldata func) external onlyOwner{
+   function execute(address dest, uint value, bytes calldata func) external {
        _requireFromEntryPointOrOwner();
        _call(dest, value, func);
    }

-   function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
+   function executeBatch(address[] calldata dest, bytes[] calldata func) external {    
        _requireFromEntryPointOrOwner();
        require(dest.length == func.length, "wrong array lengths");
        for (uint i = 0; i < dest.length;) {
            _call(dest[i], 0, func[i]);
            unchecked {
                ++i;
            }
        }
    }
```    