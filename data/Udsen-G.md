## 1. USING REDUNDANT FUNCTION TO COMPLETE THE SAME TASK. REMOVING THE FUNCTION WILL REDUCE THE BYTECODE WHICH CAN REDUCE THE DEPLOYMENT GAS COST.

    function nonce() public view virtual override returns (uint256) {
        return nonces[0];
    }

    function nonce(uint256 _batchId) public view virtual override returns (uint256) {
        return nonces[_batchId];
    } 
	
`function nonce(uint256 _batchId)` is enough to complete the task of `function nonce()` as well. We can submit `_batchId = 0` (nonce(0)) as the argument for the second function above. This implements the same logic as the first function above.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L93-L99

## 2. INCREMENTS/DECREMENTS CAN BE UNCHECKED IN FOR-LOOPS IF THEY DONOT UNDERFLOW OR OVERFLOW

      for (uint256 a = 0; a < opasLen; a++) {
		
This can be re written as follows to save gas:

     for (uint256 a = 0; a < opasLen;) {
      unchecked{ ++a;}
     }
	 
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L107

There are 3 more instances of this issue:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L112
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L128
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L134