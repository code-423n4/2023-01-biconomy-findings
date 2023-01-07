1 - SmartAccount.sol::transfer() does not validate the non-zero amount
==

The [SmartAccount.sol::transfer()](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449) should check if the amount is non-zero value.


Recommendation
--

Add a non-zero validation in the ```SmartAccount.sol::transfer()``` function.
