1 - The use of ```ecrecover()``` is susceptible to signature malleability. OpenZeppelin's ECDSA library is imported but not used for recovery. Use ECDSA's recover() function:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L347
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L350