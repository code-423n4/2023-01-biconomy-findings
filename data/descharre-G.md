## Summary
|ID     | Syntax      |  Gas saved| Instances |
|:----: | :---           |              :----:    |  :----:         |
|1       | Make nonces private|   22   | 1 |
| 2      | refundInfo.gasPrice is redundant| 18| 1 |
| 3      |Unchecked for loop| 0| 1 |

## Details
### 1 Make nonces private
Saves on average 22 gas

`mapping(uint256 => uint256) public nonces;`

[SmartAccount.sol#L55](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L55)

### 2 refundInfo.gasPrice is redundant
Saves on average 18 gas

remove the !=0 in the require statement because it's also checked in the if statement below

additionally you can put the variable initialization of payment inside the if statement so it only initializes when the statement true. This is an extra 5 gas saving

- [SmartAccount.sol#L232](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L232)
- [SmartAccount.sol#L234](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L234)

### 3  Unchecked for loop
[EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)
- [EntryPoint.sol#L93-L142](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L93-L142) handleAggregatedOps: make all for loops unchecked: on average 611 gas saved
