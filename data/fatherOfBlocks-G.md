**contracts/smart-contract-wallet/SmartAccount.sol**
- L77/110/128 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L372/374/402/409/416/429/445/456/457 - If a variable in memory is only used once, it should not be necessary to create it, it could be used directly where it is needed.

- L234 - It is not necessary to create a variable if it is only going to be used inside an if that has not yet been executed, in that case, it should be created inside the if.


**contracts/smart-contract-wallet/SmartAccountFactory.sol**
- L18 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L69/70/71 - If a variable in memory is only used once, it should not be necessary to create it, it could be used directly where it is needed.


**contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol**
- L55/56/241/242/252/253/256/269/270/418/420/473/474 - If a variable in memory is only used once, it should not be necessary to create it, it could be used directly where is needed.


**contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol**
- L16 - If a variable in memory is only used once, it should not be necessary to create it, it could be used directly where it is needed.


**contracts/smart-contract-wallet/paymasters/BasePaymaster.sol**
- L35/36/44/45 - If a variable in memory is only used once, it should not be necessary to create it, it could be used directly where it is needed.


**contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol**
- L36/37/42/49/50/66/107/108/109 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/smart-contract-wallet/libs/MultiSend.sol**
- L27 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS
