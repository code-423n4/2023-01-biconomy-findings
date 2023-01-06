1. The initializer modifier in the init function is not needed: 
```solidity
function init(
        address _owner,
        address _entryPointAddress,
        address _handler
    ) public override initializer 
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166

The following check is more than enough to prevent the "init" function from being called after initialization: 
```solidity
require(owner == address(0), "Already initialized");
```
Recommendation: Remove the initializer modifier. 

2. The following check is not needed: 
```solidity
require(address(_entryPoint) == address(0), "Already initialized");
``` 
The following check does already the job:
```solidity
require(owner == address(0), "Already initialized");
```
Recommendation: Remove this require statement