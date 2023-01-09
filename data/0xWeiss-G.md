## 1
In 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol        line 109 :

Use uncheked in for loops:

 for (uint256 i = 0; i < opslen; i++) {
                _validatePrepayment(opIndex, ops[i], opInfos[opIndex], 
     address(aggregator));
                opIndex++;
            }

AFTER:

unchecked{
 for (uint256 i = 0; i < opslen; i++) {
                _validatePrepayment(opIndex, ops[i], opInfos[opIndex], 
     address(aggregator));
                opIndex++;
            }
}

## 2
In In 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol   line 116
Change the way you add 1 to opIndex

opIndex++;

AFTER

++opIndex;

