# Summary

	[L-01] Lack of two steps procedure for new ownership
	[NC-01] RETURNDATASIZE opcode is called 3 times 
	[NC-02] Repeated function getNonce() and nonce()


# [L-01] Lack of two steps procedure for new ownership

## In SmartAccount.sol [L109](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109)

	The owner of the smart account have critical rights, (like transferring funds, executing multiples transactions and so on), 
	then it very useful to implement a two steps confirmation that gives to the owner the right to make a mistake (set an incorrect 
	owner address for example) without necessarily having a great consequence.  


### Recommandation 

![Capture d’écran du 2023-01-08 14-52-26](https://user-images.githubusercontent.com/121401405/211199798-db5bb59c-6a85-45fd-964c-6e9e605d5743.png)

	


# [NC-01] RETURNDATASIZE opcode is called 3 times 

	In Proxy.sol in the fallback function, RETURNDATASIZE opcode is called 3 times. 

## Recommendation: 
	Use intermediate variables inside the assembly in Proxy.sol contract, to avoid executing opcodes
	multiple times. 

## Instead of this:

	returndatacopy(0, 0, returndatasize())
	switch success
	case 0 { revert(0, returndatasize()) }
	default { return(0, returndatasize()) }


## Use cached variable:

	let rds := returndatasize()
	returndatacopy(0, 0, rds)
	switch success
	case 0 { revert(0, rds) }
	default { return(0, rds) }


Also present in ModuleManager [L93](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L93)

# [NC-02] Repeated function getNonce() and nonce()

### In SmartAccount.sol [L97](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L97)

	These two [ getNonce() and nonce() ] have to return the number of transaction by giving the batchId; 

	Having two functions that return the same thing is not necessary and can be confusing.

### Recommandation 

	Remove the nouce() and keep getNounce() that is more explecit 