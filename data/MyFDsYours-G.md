# Gas optimization :

# Summary

 [G-01]  Preferer using unchecked statement for values cannot over/underflow  (6)
 [G-02]  Use a pre-increment over a post-increment  (7)
 [G-03]  Public function not called by the contract should be external (3)


# [G-01] Prefer using unchecked statement for values cannot over/underflow 

    For values that cannot overflow, we must specify to the compiler to not check for arythmetic overflow,
    by doing this we save a non negligeble amount of gas, especially for long loop iteration:

## Exemple in EntryPoint.sol [L100](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100) 

    For exemple, when we're looping over 100 aggregators in the handleAggregatedOps we are going to save 18228 gas just by adding unchecked statement


## PoC

![Capture d’écran du 2023-01-08 19-54-01](https://user-images.githubusercontent.com/121401405/211213666-2969846c-02ed-47cd-adcd-60e4d706c10a.png)

                     without            with                 Saved
                    unchecked          unchecked    

    n=1              21799              21676               123*1      gas       
    n=10             23410              22180               123*10     gas       
    n=100            45448              27220               123*100    gas       
    n=1000           200632             77632               123*1000   gas  


## Occurances 
    
EntryPoint.sol : [L80](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L80)  [L100](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100)   [L107](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L107)    [L112](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L112)   [L128](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L128)  [L134](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L134)   

## [G-02]  Use a pre-increment over a post-increment  

## PoC (see below in G1)

### Using ++i over i++ saves 5 gas fee per iteration 

                       i++           ++i     
    n=1              21676        21671 gas
    n=10             22180        22130 gas
    n=100            27220        26720 gas
    n=1000           77632        72632 gas


## [G-03]  Public function not called by the contract should be external 


### In SmartAccount.sol 
getDeposit [L518](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L518)
addDeposit [L525](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L525)
withdrawDepositTo [L536](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L536)
