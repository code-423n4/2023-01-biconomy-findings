Duplicate check for non `address(0x0)` for `_handler` parameter. `if` clause not needed, already checked by `require()` beforehand.

Code Link:
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L174