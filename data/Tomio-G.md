1.
Title: Using bytes constant is more gas efficient

Reference: [Here](https://ethereum.stackexchange.com/questions/3795/why-do-solidity-examples-use-bytes32-type-instead-of-string)

Proof of Concept:
[DefaultCallbackHandler.sol#L12-L13](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L12-L13)
[SmartAccountFactory.sol#L11](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L11)
[SmartAccount.sol#L36](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L36)

Recommended Mitigation Steps:
Change it to `bytes(1..32) constant`
________________________________________________________________________

2. 
Title: Struct optimization

Proof of Concept:
[PaymasterHelpers.sol#L13-L16](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L13-L16)

Recommended Mitigation Steps:
Use elementary types or a user-defined type instead can save gas
________________________________________________________________________

3.
Title: Reduce the size of error messages (Long revert Strings)

Impact:
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

Proof of Concept:
[SmartAccount.sol#L77](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L77)
[SmartAccount.sol#L110](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L110)
[SmartAccount.sol#L128](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L128)
[VerifyingSingletonPaymaster.sol#L36-L37](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L36-L37)
[VerifyingSingletonPaymaster.sol#L42](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L42)
[VerifyingSingletonPaymaster.sol#L49-L50](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L49-L50)
[VerifyingSingletonPaymaster.sol#L66](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L66)
[VerifyingSingletonPaymaster.sol#L107-L109](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L107-L109)

Recommended Mitigation Steps:
Consider shortening the revert strings to fit in 32 bytes
________________________________________________________________________

4.
Title: abi.encode() is less efficient than abi.encodePacked()

Proof of Concept:
[SmartAccount.sol#L136](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L136)
[VerifyingSingletonPaymaster.sol#L80](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L80)
________________________________________________________________________

5.
Title: function getChainId() gas improvement on returning value

Proof of Concept:
[SmartAccount.sol#L141](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L141)

Recommended Mitigation Steps:
by set `id` in returns L#140 and delete L#141 can save gas

```
function getChainId() public view returns (uint256 id) { //@audit-info: set here
```
________________________________________________________________________

6.
Title: Using multiple `require` instead `&&` can save gas

Proof of Concept:
[ModuleManager.sol#L34](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34)
[ModuleManager.sol#L49](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49)
[ModuleManager.sol#L68](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68)

Recommended Mitigation Steps:
Change to:

```
	require(module != address(0), "BSA101");
	require(module != SENTINEL_MODULES, "BSA101");
```
________________________________________________________________________