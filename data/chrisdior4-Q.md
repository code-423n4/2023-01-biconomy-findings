# QA report

## Low Risk
| L-R    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Use the latest version of OpenZeppelin | 1 |
| [L&#x2011;02] | Unused/empty receive()/fallback() function | 1 |
| [L&#x2011;03] | Avoid the use of tx.origin | 1 |

Total: 3 instances over 3 issues

## Non-critical

| N-C   |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Event is missing indexed fields | 2 |
| [N&#x2011;02] | Commented out code | 2 |
| [N&#x2011;03] | Use the latest Solidity version | 1 |
| [N&#x2011;04] | Constants should be defined rather than using magic numbers | 2 |

Total: 7 instances over 4 issues

## Low Risk

# [L-01] Use the latest version of OpenZeppelin

To prevent any issues in the future (e.g. using solely hardhat to compile and deploy the contracts), upgrade the used OZ packages within the package.json to the latest versions.

```solidity
"@openzeppelin/contracts": "^4.7.3",
```

### Recommended Mitigation Steps:

Consider using the latest OZ packages (v4.8.0) within package.json.


# [L-02] Unused/empty receive()/fallback() function
Sometimes Ether might be mistakenly sent to the contract. It's better to prevent this from happening.
smartAccount.sol
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert
If a method does not have an external call then it is impossible to reenter, so you can skip this modifier in such methods

File: SmartAccount.sol

```solidity
    receive() external payable {}
```

Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550

# [L-03] Avoid the use of tx.origin

The tx.origin environment variable has been found to influence a control flow decision. Note that using tx.origin as a security control might cause a situation where a user inadvertently authorizes a smart contract to perform an action on their behalf. It is recommended to use msg.sender instead.

File: SmartAccount.sol

```solidity
address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
```

Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L257


## Non-critical

### \[NC-01\] Event is missing indexed fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

File: SmartAccount.sol

```solidity
event ImplementationUpdated(address _scw, string version, address newImplementation);
event EntryPointChanged(address oldEntryPoint, address newEntryPoint);
```

Lines of code: 

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L64

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L65

### \[NC-02\] Commented out code 
I suggest removing the commented-out code or adding an explanation.

File: SmartAccount.sol

```solidity
// uint256 extraGas;
```

```solidity
// extraGas = extraGas - gasleft();
```

Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L235

- - https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L242

### \[NC-03\] Use the latest Solidity version

The contract is using 0.8.12, upgrade it to 0.8.17.

 ### \[NC-04\] Constants should be defined rather than using magic numbers

File: SmartAccount.sol

```solidity
uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
```

```solidity
require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
```

Lines of code:

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L200

- https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L224

