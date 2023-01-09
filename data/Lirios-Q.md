### [N-01] SmartAccount.init Incorrect errormessage handler validation
The errormessage of the handler validation is incorrect. 

        require(_handler != address(0), "Invalid Entrypoint");

Should be 

        require(_handler != address(0), "Invalid handler");

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171 




### [N-02] SmartAccount.init double handler != 0 check
In the init function, we see the following line:  

        if (_handler != address(0)) internalSetFallbackHandler(_handler);

Af few lines above is 

        require(_handler != address(0), "Invalid Entrypoint");

If _handler would be address(0) the require would have reverted the transaction and never reached the if statement. This makes the if statement redundant and not needed, it can be replaced by
	  internalSetFallbackHandler(_handler);
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L174



### [N-03] SmartAccount.execute Confusing, unneeded _requireFromEntryPointOrOwner call
execute function calls _requireFromEntryPointOrOwner, which implies that the function can be called from owner or entrypoint. The function has an onlyOwner modifier. Because of the onlyOwner modifier, it can only be called by owner and the _requireFromEntryPointOrOwner will always return true, so should be removed. 
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L461



### [N-04] SmartAccount.executeBatch Confusing, unneeded _requireFromEntryPointOrOwner call
executeBatch function calls _requireFromEntryPointOrOwner, which implies that the function can be called from owner or entrypoint. The function has an onlyOwner modifier. Because of the onlyOwner modifier, it can only be called by owner and the _requireFromEntryPointOrOwner will always return true, so should be removed. 
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L466