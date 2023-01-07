### Code examples are truncated for brevity and correlate to the first linked line following each block.

# USE MULTIPLE REQUIRE STATEMENTS RATHER THAN {&&}

    require(module != address(0))
    require(module != SENTINEL_MODULES, "BSA101");

[ModuleManager.sol:L#34](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34)
[ModuleManager.sol:L#49](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49)
[ModuleManager.sol:L#68](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68)


# REFACTOR DUPLICATED REQUIRE() AND REVERT() CHECKS

    function modCheck1() private view {
        //require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
        require(module != address(0))
        require(module != SENTINEL_MODULES, "BSA101");
    }

    function enableModule(address module) public authorized {
        modCheck1();
    }
    function disableModule(address prevModule, address module) public authorized {
        modCheck1();
    }

[ModuleManager.sol:L#34+49](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34)
[SmartAccount.sol:L#262+286](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L262)
[SmartAccount.sol:L#265+289](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L265)
[SmartAccount.sol:L#294+374](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L294)
[SmartAccount.sol:L#452+491](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L452)
[SmartAccount.sol:L#348+351](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L348)
[StakeManager.sol:L#64+99](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L64)
[StakeManager.sol:L#107+121](https://github.com/code-423n4/2023-01-biconomy/blob/5df2e8f8c0fd3393b9ecdad9ef356955f07fbbdd/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L107)


# USE FUNCTIONS INSTEAD OF MODIFIERS

    function onlyOwner() private view {
        require(msg.sender == owner, "Smart Account:: Sender is not authorized");
     }

    function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public {
        onlyOwner();
        entryPoint().withdrawTo(withdrawAddress, amount);
    }

[SmartAccount.sol:L#76](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L76)
[SmartAccount.sol:L#82](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L82)
[SmartAccount.sol:L#88](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L88)


# USE ASSEMBLY WHEN WRITING TO STORAGE VALUES

	function setOwner(address _newOwner) external mixedAuth {
    assembly {
        sstore(owner.slot, _newOwner)
    }
	}

[SmartAccount.sol:L#112](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L112)
[SmartAccount.sol:L#172](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L172)