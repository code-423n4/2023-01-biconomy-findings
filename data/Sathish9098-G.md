##

## [GAS-1]  USE FUNCTION INSTEAD OF MODIFIERS . Its possible to save more gas . 

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

        modifier onlyOwner {
        require(msg.sender == owner, "Smart Account:: Sender is not authorized");
        _;
          }

        modifier mixedAuth {
        require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
        _;
        }

        modifier onlyEntryPoint {
        require(msg.sender == address(entryPoint()), "wallet: not from EntryPoint");
        _; 
        }


 




GAS-1	Use assembly to check for address(0)	27
GAS-2	Using bools for storage incurs overhead	1
GAS-3	Cache array length outside of loop	1
GAS-4	State variables should be cached in stack variables rather than re-reading them from storage	1
GAS-5	Use calldata instead of memory for function arguments that do not get mutated	16
GAS-6	Use Custom Errors	71
GAS-7	Don't initialize variables with default value	19
GAS-8	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	12
GAS-9	Using private rather than public for constants, saves gas	4
GAS-10	Use != 0 instead of > 0 for unsigned integer comparison	24
GAS-11	internal functions not called by the contract should be removed	8