### [G-01] REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met. Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

 **scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol**

77: `require(msg.sender == owner, "Smart Account:: Sender is not authorized");`
110:  `scw-contracts/contracts/smart-contract-wallet/SmartAccount.solscw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol`
27:  `require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");`

 **scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol**

107:  `require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");`
108:  `require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");`
109:  `require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");`

### [G-02] USE NAMED RETURNS WHERE APPROPRIATE
**scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol**

140:   `function getChainId() public view returns (uint256) {
        uint256 id;
        // solhint-disable-next-line no-inline-assembly
        assembly {
            id := chainid()
        }
        return id;
    }`

### [G-03] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS
Instead of using the && operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement (saving 3 gas per &):

**scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol**

34,49:  `require(module != address(0) && module != SENTINEL_MODULES, "BSA101");`
68:  `require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");`
