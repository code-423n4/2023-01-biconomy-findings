### [N-01] USE A MORE RECENT VERSION OF SOLIDITY
Old version of Solidity is used 0.8.12, newer version can be used 0.8.17

### [N-02 ] INCONSISTENT SOLIDITY VERSIONS

**scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol**

### [N-03 ] AVOID FLOATING PRAGMAS: THE VERSION SHOULD BE LOCKED

**scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol
scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol
scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol
scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol
scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol
scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol
scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol
scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol
scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol
scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol**

### [N-04] FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES
The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace. Instead, the Solidity docs recommend specifying imported symbols explicitly. 
### [N-05]  NON-ASSEMBLY METHOD AVAILABLE
There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it’s best to avoid using it where it’s not necessary
144: **scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol**
`assembly{ id := chainid() }
 => uint256 id = block.chainid`

### [L-01] NO TRANSFER OWNERSHIP PATTERN
Recommend considering implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

**scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol**

### [L-02] DUPLICATED REQUIRE()/REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION
**scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol**
34,49:  `require(module != address(0) && module != SENTINEL_MODULES, "BSA101");`


### [L‑03] Unused/empty receive()/fallback() function
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

**scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol
scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol**


### [L‑04]  AVOID NESTED IF BLOCKS
**scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol** 
261-272:

           function _createSenderIfNeeded(uint256 opIndex, UserOpInfo memory opInfo, bytes calldata initCode) internal {

           if (initCode.length != 0) {

            address sender = opInfo.mUserOp.sender;

            if (sender.code.length != 0) revert FailedOp(opIndex, address(0), "AA10 sender already constructed");

            address sender1 = senderCreator.createSender{gas : opInfo.mUserOp.verificationGasLimit}(initCode);

            if (sender1 == address(0)) revert FailedOp(opIndex, address(0), "AA13 initCode failed or OOG");

            if (sender1 != sender) revert FailedOp(opIndex, address(0), "AA14 initCode must return sender");

            if (sender1.code.length == 0) revert FailedOp(opIndex, address(0), "AA15 initCode must create sender");

            address factory = address(bytes20(initCode[0 : 20]));

            emit AccountDeployed(opInfo.userOpHash, sender, factory, opInfo.mUserOp.paymaster);
        }
    }
