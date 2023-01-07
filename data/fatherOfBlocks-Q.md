**contracts/smart-contract-wallet/SmartAccount.sol**
- L109/120/127 - The mixedAuth modifier is used in the setOwner function, the problem with this is that within this modifier it is validated: msg.sender == owner || msg.sender == address(this).
But the second validation is unnecessary since it is impossible for the msg.sender to be address(this), this is so because there is no function within the contract that allows calling the setOwner function.
The same is true for updateImplementation and updateEntryPoint.
Also, since there is no function in which it is useful to use this modifier, it should be removed.

- L371/528 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.

- L198/199/201/237/238/242/243 - There is commented code that is not used, this should be eliminated since it does not add anything to the code.


**contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol**
- By convention, contracts that are interfaces begin with I, but in this case that is not respected. In this case, IERC1155TokenReceiver.


**contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol**
- By convention, contracts that are interfaces begin with I, but in this case that is not respected. In this case, IERC721TokenReceiver.


**contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol**
- By convention, contracts that are interfaces begin with I, but in this case that is not respected. In this case, IERC777TokenReceiver.


**contracts/smart-contract-wallet/libs/Math.sol**
- L78 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.

- L74 - It is not validated that the dominator is != 0, this is important since if a zero is set as input it would revert the function without knowing why.


**contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol**
- L17 - The Exec library is imported, but it is never used, therefore it should be removed.


**contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol**
- L4/6 - The UserOperation and IAggregator classes are imported but they are never so used, therefore the import should be removed.


**contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol**
- The contract is in a folder for interfaces, since it is a UserOperationLib library. The correct thing would be that it is in another folder.


**contracts/smart-contract-wallet/paymasters/BasePaymaster.sol**
- L105 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol**
- L7 - The Initializable contract is imported, but it is never used, therefore it should be deleted.

- L10 - There is commented code that is not used, this should be eliminated since it does not add anything to the code.
