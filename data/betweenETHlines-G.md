1) Early revert in `_payPrefund` saves some gas 

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L108

the `_payPrefund` function is called within `validateUserOp` which is called by `entryPoint` within `_validateAccountPrepayment`. If the call within `_payPrefund` reverts/fails, it will revert in line 334: `https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L334`

Reverting earlier will therefore save some gas.

*As with all logic changes, this implementation needs careful validation to not collide with any other logic.

2) Removal of unnecessary `assert` will save some gas

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L334

Since the `_IMPLEMENTATION_SLOT` is defined as a constant variable, the assert is not necessary. Consider removing it.

* The same issue applies for the `Singleton` contract:

`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13`

3) Event for `setOwner` can be emitted earlier to save memory caching

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109

Consider emitting the event before the owner is set, that way, the event can be emitted with (owner, _newOwner). That will save some gas and is consistent with other functions e.g. `updateEntryPoint`.
