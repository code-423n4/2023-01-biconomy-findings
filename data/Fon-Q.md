function nonce
```solidity
function nonce(uint256 _batchId) public view virtual override returns (uint256) {
        return nonces[_batchId];
    }
```
and function getNonce
```solidity
function getNonce(uint256 batchId)
    public view
    returns (uint256) {
        return nonces[batchId];
    }
```
do exactly the same thing, consider removing getNonce

