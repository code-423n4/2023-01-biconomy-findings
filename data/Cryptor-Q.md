# Deploy functions does not check if _owner is an EOA or a contract 

1.  https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L53 

Comments say that the parameter _owner must be an EOA, listed here. 

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L49

but the code does not check for that requirement, meaning that a smart contract can be the owner of the deployed wallet. Funds could be lost and the protocol may not work properly in some instances if the smart contract were to have a self destruct function. Also, if the smart contract is vulnerable, a malicious actor can take control of the smart contract and thus take control of the user's deployed wallet.







 2. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L33 

Same issue as previous. The function does not check if _owner parameter is an EOA


#### Suggestion 

While not being 100% reliable, adding isContract(account) from OpenZepplin may mitigate this issue 
