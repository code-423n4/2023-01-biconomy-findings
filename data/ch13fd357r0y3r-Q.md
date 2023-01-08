**Remove the unused mapping for `isAccountExist[]`**

## Permalinks
https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L44

https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L60

The `isAccountExist[]` is not used anywhere in the SmartAccountFactory, Because the `create` and `create2` produces different proxy based on various inputs and caller.  So It's Better to remove from the code for QA and gas opt.
