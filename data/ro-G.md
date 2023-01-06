1. The initializer modifier in the init function is not needed: 
```solidity
function init(
        address _owner,
        address _entryPointAddress,
        address _handler
    ) public override initializer 
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166

The following check is more than enough to prevent the "init" function from being called after initialization: 
```solidity
require(owner == address(0), "Already initialized");
```
Recommendation: Remove the initializer modifier. 

2. The following check is not needed: 
```solidity
require(address(_entryPoint) == address(0), "Already initialized");
``` 
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L168
The following check does already the job:
```solidity
require(owner == address(0), "Already initialized");
```
Recommendation: Remove this require statement

3. Uncessary use of "nonReentrant" 
  The transfer function uses the "nonReentrant" modifier: 
  ```solidity
 function transfer(address payable dest, uint amount) external nonReentrant onlyOwner
````
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449
This causes an "sload" every time the function gets called, and it is not necessary because only the owner is allowed to call this function, therefore a malicious contract cannot re-enter.

4. Uncessary use of "nonReentrant" 
  The handlePayment function uses the "nonReentrant" modifier: 
  ```solidity
 function handlePayment(
        uint256 gasUsed,
        uint256 baseGas,
        uint256 gasPrice,
        uint256 tokenGasPriceFactor,
        address gasToken,
        address payable refundReceiver
    ) private nonReentrant
````
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L247
This causes an "sload" every time the function gets called, and it is not necessary because the nonce should be increased in the "execTransaction" function, therefore invalidating the current signatures.
