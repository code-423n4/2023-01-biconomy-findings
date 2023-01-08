1) SmartAccount.sol - Require Strings More than 32 bytes -  4 instances 

Saves 3 gas if string length is 32 characters or less

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L77-L78

```solidity
        require(msg.sender == owner, "Smart Account:: Sender is not authorized");

```

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L110-L111

```solidity
        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");

```

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L128-L129

```solidity
        require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
        require(Address.isContract(_newEntryPoint), "Smart Account:: new entry point address must be a contract"
```