## [G-01] Splitting require() statements that use && saves gas

### IMPACT

Require statements including conditions with the && operator can be broken down in
multiple require statements to save gas.

### POC 


```
contracts/smart-contract-wallet/base/ModuleManager.sol:34:        require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
contracts/smart-contract-wallet/base/ModuleManager.sol:49:        require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
contracts/smart-contract-wallet/base/ModuleManager.sol:68:        require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```

### MITIGATION

Breakdown each condition in a separate require
```console
require(module != address(0),"BSA101");
require(module != SENTINEL_MODULES, "BSA101");)
``` 
