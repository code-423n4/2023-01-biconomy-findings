## 1. USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

Can import the required specific contracts, functions or variables by using the named imports explicitly. Plain imports will import the entire context of the imported contract which could lead into variable name conflicts etc ...

Currently the Enum is imported as follows:

	import "./common/Enum.sol";

But it can be imported explicitly by the name as follows:

	import {Enum} from "./common/Enum.sol";

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L10

There are 15 more instances of this issue:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L4
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L8-L15
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L4-L5
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L4
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L8-L15
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol#L8-L9
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L7-L9
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L12-L19
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L4
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L4
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L4
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L4-L6
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L4-L7
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L5
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L9


## 2. MISSING `address(0)` CHECK FOR THE `_implementation` ADDRESS.

_implementation variable on https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L15 could be set as address(0) by mistake.
There is no way to change this since this is the constructor and will be called only once during the deployment of the contract.

Recommended to do `address(0)` check as follows:

	require(_implementation != address(0), "Address cannot be zero");
	
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L15-L20
	
## 3.`assert()` FUNCTION IS USED WITHOUT DESCRIPTION MESSAGE FOR THE REVERT.

         assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));

Add a description message to the assert function for the revert. It is very important to add a revert message, then `msg.sender` has enough information to learn the reason of failure.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16

There is 1 more instance of this issue:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13

## 4. MISSING `address(0)` CHECKS FOR THE FOLLOWING ADDRESS INPUTS TO THE FUNCTIONS.

    function updateImplementation(address _implementation) external mixedAuth {
        require(_implementation.isContract(), "INVALID_IMPLEMENTATION"); //@audit - does this check for zero address
        _setImplementation(_implementation);

By mistake _implementation could be set to address(0). Hence it is a good practice to check for address(0).	

	require(_implementation != address(0), "Address cannot be zero");
	
It is recommended to check for the address(0) for the _implementation variable before `_setImplementation(_implementation)` is called.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L120-L122

There are 3 more instances of this issue:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L446
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L176
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L120

## 5. CONDUCTING REDUNDANT CHECKS FOR THE SAME CONDITION ON `_handler` PARAMETER.

        require(_handler != address(0), "Invalid Entrypoint");
        owner = _owner;
        _entryPoint =  IEntryPoint(payable(_entryPointAddress));
        if (_handler != address(0)) internalSetFallbackHandler(_handler); 
		
In the above code the `require(_handler != address(0), "Invalid Entrypoint");` checks for the zero address condition. Hence there is no need to check the same condition using the if conditional check `if (_handler != address(0))`

Above given code can be changed as follows:

        require(_handler != address(0), "Invalid Entrypoint");
        owner = _owner;
        _entryPoint =  IEntryPoint(payable(_entryPointAddress));
        internalSetFallbackHandler(_handler); 
		
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171-L174

There is 1 more instance of this issue:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L171-L174


## 6. CHECK FOR THE CONTRACT EXISTENCE USING `extcodesize` IF LOW LEVEL `call` FUNCTION IS USED TO SEND ETHER TO CONTRACTS WITH `recieve()` FUNCTIONS.

            (bool success,) = receiver.call{value: payment}(""); 
            require(success, "BSA011");
			
In the above code if the receiver is a contract, and if does not include any code in it, it will still return `success = true`. 
Hence it is best practice to check for the contract existence before sending ether to a contract using low level `call()` function

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L261-L262

There are 6 more instances of this issue:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L261
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L284
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L441
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L468
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L37
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L120

## 7. PROPER COMMENTING WILL INCREASE THE READABILITY OF THE CODE.

	// This check is not completely accurate, since it is possible that more signatures than the threshold are send.
	
The word "send" should be corrected as "sent", in the above comment.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L320

    /// @param targetTxGas Fas that should be used for the safe transaction. 

The word "Fas" should be corrected as "Gas" in the above comment.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L382
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L372

    ///      Since the `estimateGas` function includes refunds, call this method to get an estimated of the costs that are deducted from the safe with `execTransaction` 
	
The word "estimated" should be corrected as "estimate" in the above comment.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L352	

     * @param opIndex into into the opInfo array 
	 
Remove the extra "into" word from the comment.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L43

        // 0xa9059cbb - keccack("transfer(address,uint256)")
		
The comment should be changed to `bytes4(keccack("transfer(address,uint256)"))` since it denotes the function selector correctly.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L15 

## 8. THE FUNCTION `_requireFromEntryPointOrOwner()` CAN BE CONVERTED TO A MODIFIER FOR BETTER CODE READABILITY.

The `_requireFromEntryPointOrOwner()` function does access control checks using the `require()` statement. Hence this function can be changed into a modifier and added to functions for better code readability.

    function execute(address dest, uint value, bytes calldata func) external onlyOwner{
        _requireFromEntryPointOrOwner();
		
    function _requireFromEntryPointOrOwner() internal view {
        require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
    }
	
The above function can be changed into a modifier code as follows:

    modifier requireFromEntryPointOrOwner{
        require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
		_;
    }
	
And can be added to the `execute()` function as follows:

    function execute(address dest, uint value, bytes calldata func) external onlyOwner requireFromEntryPointOrOwner {
        _requireFromEntryPointOrOwner();
	
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L494-L496
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460-L461

## 9. CAN CHANGE THE VARIABLE NAME `interfaceId` to `_interfaceId`.

    function supportsInterface(bytes4 interfaceId) external view virtual override returns (bool) {
        return interfaceId == type(IERC165).interfaceId; // 0x01ffc9a7
    } 
	
In the above code `interfaceId` can be changed to `_interfaceId`, since `type(IERC165).interfaceId` is also called. Changed code will look as follows:

    function supportsInterface(bytes4 _interfaceId) external view virtual override returns (bool) {
        return _interfaceId == type(IERC165).interfaceId; // 0x01ffc9a7
    } 
	
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L545-L547

## 10. REQUIRE STATEMENT ERROR MESSAGE SHOULD BE CHANGED TO A MEANINGFUL DESCRIPTION OF THE ERROR MESSAGE.

        require(_handler != address(0), "Invalid Entrypoint");
		
The require statement is used to check for the invalid `_handler` address. Hence it is best practice to change the description of the error message as follows:

        require(_handler != address(0), "Invalid Handler");
		
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L171

There is 1 more instance of this issue:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171

## 11. REQUIRE CONDITION ALWAYS RESULTS IN `true`.

            require(_signer == owner || true, "INVALID_SIGNATURE");
			
In the above condition the require statement does a "OR" operation with the `true`. Hence condition always results in true. This can be a coding error or if not `require` function can be removed.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L343
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L346

## 12. EVENTS HAVE ALREADY BEEN EMITTED, EVEN BEFORE THE STATE CHANGES AND CONDITIONAL CHECKS HAVE BEEN COMPLETED INSIDE FUNCTIONS.

The emitted events will not revert even if the function reverts if conditional checks fail and transaction reverts.

        emit StakeWithdrawn(msg.sender, withdrawAddress, stake);
        (bool success,) = withdrawAddress.call{value : stake}(""); 
        require(success, "failed to withdraw stake"); 
		
The `require` function can revert, but the `StakeWithdrawn` event has already been emitted.
It is recommended to emit the event ones all the state changes and conditional checks have been completed. 

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L105-L107

There is 1 more instance of this issue:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L119

## 13. TRANSACTION GAS LIMIT IS HARD CODED TO `21,000`. THIS CAN BE CHANGED IN THE FUTURE DUE TO ETHEREUM HARD FORK OR OTHER PROTOCOL CHANGES.

        uint256 startGas = gasleft() + 21000 + msg.data.length * 8; 
		
In the above line of code, to calculate the `startGas` the transaction gas limit is hardcoded as `21000`.
But in the future due to hard forks or other protocol changes in the ethereum network the default trasaction gas limit of `21,000` could change.
This could impact all the gas calculations based on the above `startGas` calculation. Hence could impact the transactions based on this gas calculation.

It is recommended to use a variable for the transaction gas limit instead of hardcoding it to `21,000`. And should implement a setter function to update the transaction gas limit if a change happens to it in the future.

There are two instances of this issue.
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L200
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L200