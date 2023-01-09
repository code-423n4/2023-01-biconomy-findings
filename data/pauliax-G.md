* Maybe this could be extracted to a constant because this data does not change:
```solidity
  bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
```

* I think ```PaymasterHelpers``` does not actually use ```ECDSA``` library:
```solidity
  library PaymasterHelpers {
    using ECDSA for bytes32;
```