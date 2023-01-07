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

##

## [GAS-2]   Use uint8,uint64 Can Increase Gas Cost. Uint256 consume less gas than uint8 in function parameters 

A smart contract's gas consumption can be higher if developers use items that are less than 32 bytes in size because the Ethereum Virtual Machine can only handle 32 bytes at a time. In order to increase the element's size to the necessary size, the EVM has to perform additional operations

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

      307 :  uint8 v;


[FILE : 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol)

      84:  uint64 withdrawTime = uint64(block.timestamp) + info.unstakeDelaySec;

##

## [GAS-3]  NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

      function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)
        internal override virtual returns (uint256 deadline) {
        bytes32 hash = userOpHash.toEthSignedMessageHash();
        //ignore signature mismatch of from==ZERO_ADDRESS (for eth_callUserOp validation purposes)
        // solhint-disable-next-line avoid-tx-origin
        require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
        return 0;
        }

(https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L55-L135)

(https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L168-L190)

[FILE : 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol)

       function getDepositInfo(address account) public view returns (DepositInfo memory info) {
        return deposits[account];
    }


##

## [GAS-4]   ++I/I++ OR --I/I-- SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} OR  UNCHECKED{--I}/UNCHECKED{I--}WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS


[FILE : 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)

           112:   for (uint256 i = 0; i < opslen; i++) {

           107 :   for (uint256 a = 0; a < opasLen; a++) {

           128:    for (uint256 a = 0; a < opasLen; a++) {

           134:    for (uint256 i = 0; i < opslen; i++) {

##

## [GAS-5]  CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS.ITS POSSIBLE TO SAVE 10 GAS FOR EVERY ITERATIONS .

[FILE : 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)   

             for (uint256 a = 0; a < opasLen; a++) {
            UserOpsPerAggregator calldata opa = opsPerAggregator[a];  //@Audit Variable declared inside the loop  
            UserOperation[] calldata ops = opa.userOps;  //@Audit Variable declared inside the loop  
            IAggregator aggregator = opa.aggregator;  //@Audit Variable declared inside the loop  
            uint256 opslen = ops.length;   //@Audit Variable declared inside the loop  
            for (uint256 i = 0; i < opslen; i++) {
                _validatePrepayment(opIndex, ops[i], opInfos[opIndex], address(aggregator));
                opIndex++;
            }

            if (address(aggregator) != address(0)) {
                // solhint-disable-next-line no-empty-blocks
                try aggregator.validateSignatures(ops, opa.signature) {}
                catch {
                    revert SignatureValidationFailed(address(aggregator));
                }
            }
        }

            for (uint256 a = 0; a < opasLen; a++) {
            UserOpsPerAggregator calldata opa = opsPerAggregator[a];   //@Audit Variable declared inside the loop  
            emit SignatureAggregatorChanged(address(opa.aggregator));   //@Audit Variable declared inside the loop  
            UserOperation[] calldata ops = opa.userOps;       //@Audit Variable declared inside the loop  
            uint256 opslen = ops.length;   //@Audit Variable declared inside the loop  

            for (uint256 i = 0; i < opslen; i++) {
                collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);
                opIndex++;
            }
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