# Number 1: Floating Pragma

Vulnerability details

Impact

Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.8.12, ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

For reference, see https://swcregistry.io/docs/SWC-103
Proof of Concept

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L2

Tools Used

Manual Analysis

Recommended Mitigation Steps

Remove ^ in “pragma solidity ^0.8.12” and change it to “pragma solidity 0.8.12” to be consistent with the rest of the contracts.

# Number 2: Use "interfaceId" instead of hex number

Vulnerability details
Impact
In the contract DefaultCallbackHandler.sol a few function returns are encoded as hexadecimal numbers.
Solidity also has the keyword "interfaceId" which makes the code easier to read and less error-prone.

Note: SmartAccount.sol already uses this construct:
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L546

Proof of Concept
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L22

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L32

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L41

    ) external pure override returns (bytes4) {
        return 0xf23a6e61;

    ) external pure override returns (bytes4) {
        return 0xbc197c81;  

    ) external pure override returns (bytes4) {
        return 0x150b7a02;


Tools Used
Editor

Recommended Mitigation Steps
Alternative implementations:

interfaceId == type(ERC1155TokenReceiver).interfaceId // return  0xf23a6e61;
interfaceId == type(ERC1155TokenReceiver).interfaceId // return 0xbc197c81;
interfaceId == type(ERC721TokenReceiver).interfaceId // return 0x150b7a02;


