QA1. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L397
There is a typo here, the comparison should be against ``type(uint128).max`` instead of ``type(uint120).max`` since the documentation says "validate all numeric values in userOp are well below 128 bit".

```
require(maxGasValues <= type(uint128).max, "AA94 gas values overflow");

```