1. onlyOwner and _requireFromEntryPointOrOwner(); 
  have clashing authorization.

 onlyOwner modifier prevents entryPoint from calling the function

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465

remove the "onlyOwner " modifier since  _requireFromEntrypointOrOwner() allows both entryPoint and onlyOwner to call the function