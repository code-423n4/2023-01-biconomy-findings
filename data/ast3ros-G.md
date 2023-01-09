## [GAS-1] Initiate the constant _IMPLEMENTATION_SLOT to save assert operation
Initiate the `bytes32 internal constant _IMPLEMENTATION_SLOT = bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1)` and remove assert `assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));` to save gas. The assert operation is unnecessary.

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L11
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16

## [GAS-2] Unneccessary require/condition operations & statements should be removed
Two require `require(owner == address(0), "Already initialized");` and `address(_entryPoint) == address(0), "Already initialized"` can be removed because the init function already has `initializer` modifier that prevents reinitialize.

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L167-L168

`_handler != address(0)` is always true:
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L174

`_requireFromEntryPointOrOwner();` is always true because it has the onlyOwner modifier:
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L461
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L466

`requiredPreFund` variable is used, no need to silence compiler warning about unused variable
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L99

`assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));` is always true.
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L10

## [GAS-3] Upgrade to compiler at least 0.8.4

Upgrade to the lastest compiler help optimization. For example:

[Optimizer improvements in packed structs](https://blog.soliditylang.org/2021/03/23/solidity-0.8.3-release-announcement/#optimizer-improvements):
Before 0.8.3, storing packed structs, in some cases used an additional storage read operation. After EIP-2929, if the slot was already cold, this means unnecessary stack operations and extra deploy time costs. However, if the slot was already warm, this means additional cost of 100 gas alongside the same unnecessary stack operations and extra deploy time costs.

[Custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) from 0.8.4, leads to cheaper deploy time cost and run time cost. Note: the run time cost is only relevant when the revert condition is met. In short, replace revert strings by custom errors.