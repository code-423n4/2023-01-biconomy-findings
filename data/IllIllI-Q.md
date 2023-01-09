

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Upgradeable contract not initialized | 1 | 
| [L&#x2011;02] | Not all parent contracts are upgradeable | 4 | 
| [L&#x2011;03] | Loss of precision | 1 | 
| [L&#x2011;04] | Signatures vulnerable | 1 | 
| [L&#x2011;05] | Empty `receive()`/`payable fallback()` function does not authorize requests | 1 | 
| [L&#x2011;06] | `require()` should be used instead of `assert()` | 2 | 
| [L&#x2011;07] | Open TODOs | 1 | 

Total: 11 instances over 7 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 1 | 
| [N&#x2011;02] | Import declarations should import specific identifiers, rather than the whole file | 54 | 
| [N&#x2011;03] | Missing `initializer` modifier on constructor | 1 | 
| [N&#x2011;04] | Unused file | 2 | 
| [N&#x2011;05] | Adding a `return` statement when the function defines a named return variable, is redundant | 1 | 
| [N&#x2011;06] | `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings | 3 | 
| [N&#x2011;07] | Non-assembly method available | 2 | 
| [N&#x2011;08] | `constant`s should be defined rather than using magic numbers | 192 | 
| [N&#x2011;09] | Missing event and or timelock for critical parameter change | 2 | 
| [N&#x2011;10] | Events that mark critical parameter changes should contain both the old and the new value | 3 | 
| [N&#x2011;11] | Use a more recent version of solidity | 5 | 
| [N&#x2011;12] | Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`) | 13 | 
| [N&#x2011;13] | Constant redefined elsewhere | 2 | 
| [N&#x2011;14] | Inconsistent spacing in comments | 30 | 
| [N&#x2011;15] | Lines are too long | 12 | 
| [N&#x2011;16] | Non-library/interface files should use fixed compiler versions, not floating ones | 2 | 
| [N&#x2011;17] | Typos | 3 | 
| [N&#x2011;18] | Misplaced SPDX identifier | 2 | 
| [N&#x2011;19] | File is missing NatSpec | 7 | 
| [N&#x2011;20] | NatSpec is incomplete | 17 | 
| [N&#x2011;21] | Not using the named return variables anywhere in the function is confusing | 5 | 
| [N&#x2011;22] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 5 | 
| [N&#x2011;23] | Consider using `delete` rather than assigning zero to clear values | 3 | 
| [N&#x2011;24] | Avoid the use of sensitive terms | 87 | 
| [N&#x2011;25] | Large assembly blocks should have extensive comments | 5 | 
| [N&#x2011;26] | Contracts should have full test coverage | 1 | 
| [N&#x2011;27] | Large or complicated code bases should implement fuzzing tests | 1 | 
| [N&#x2011;28] | Function ordering does not follow the Solidity style guide | 31 | 
| [N&#x2011;29] | Contract does not follow the Solidity style guide's suggested layout ordering | 3 | 
| [N&#x2011;30] | Interfaces should be indicated with an `I` prefix in the contract name | 3 | 

Total: 498 instances over 30 issues





## Low Risk Issues

### [L&#x2011;01]  Upgradeable contract not initialized
Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit missing __ReentrancyGuard_init()
18:   contract SmartAccount is 

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L18

### [L&#x2011;02]  Not all parent contracts are upgradeable
Upgrades will break if the contracts in question have variables whose values are initialized in the constructor, or have variables where initial values are [set in field declarations](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#avoid-initial-values-in-field-declarations)

*There are 4 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit ModuleManager
/// @audit SignatureDecoder
/// @audit SecuredTokenTransfer
/// @audit FallbackManager
18:   contract SmartAccount is 

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L18

### [L&#x2011;03]  Loss of precision
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

288:              payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L288

### [L&#x2011;04]  Signatures vulnerable 
`ecrecover()` accepts as valid, two versions of signatures, meaning an attacker can use the same signature twice. Consider adding checks for signature malleability, or using OpenZeppelin's `ECDSA` library to perform the extra checks necessary in order to prevent this attack. In addition, non-zero must be checked for as the resulting signer, or else invalid signatures may be useable

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

350:              _signer = ecrecover(dataHash, v, r, s);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L350

### [L&#x2011;05]  Empty `receive()`/`payable fallback()` function does not authorize requests
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. `require(msg.sender == address(weth))`). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

550:      receive() external payable {}

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550

### [L&#x2011;06]  `require()` should be used instead of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

*There are 2 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol

13:           assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13

```solidity
File: scw-contracts/contracts/smart-contract-wallet/Proxy.sol

16:            assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16

### [L&#x2011;07]  Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

255:          // TODO: copy logic of gasPrice?

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255

## Non-critical Issues

### [N&#x2011;01]  Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

18    contract SmartAccount is 
19         Singleton,
20         BaseSmartAccount,
21         IERC165,
22         ModuleManager,
23         SignatureDecoder,
24         SecuredTokenTransfer,
25         ISignatureValidatorConstants,
26         FallbackManager,
27         Initializable,
28:        ReentrancyGuardUpgradeable

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L18-L28

### [N&#x2011;02]  Import declarations should import specific identifiers, rather than the whole file
Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation

*There are 54 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

12:   import "../interfaces/IAccount.sol";

13:   import "../interfaces/IPaymaster.sol";

15:   import "../interfaces/IAggregatedAccount.sol";

16:   import "../interfaces/IEntryPoint.sol";

17:   import "../utils/Exec.sol";

18:   import "./StakeManager.sol";

19:   import "./SenderCreator.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L12

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

4:    import "../interfaces/IStakeManager.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol

4:    import "./UserOperation.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol

4:    import "./UserOperation.sol";

5:    import "./IAccount.sol";

6:    import "./IAggregator.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol

4:    import "./UserOperation.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol

12:   import "./UserOperation.sol";

13:   import "./IStakeManager.sol";

14:   import "./IAggregator.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L12

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol

4:    import "./UserOperation.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/Executor.sol

4:    import "../common/Enum.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

4:    import "../common/SelfAuthorized.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

4:    import "../common/Enum.sol";

5:    import "../common/SelfAuthorized.sol";

6:    import "./Executor.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

8:    import "@account-abstraction/contracts/interfaces/IAccount.sol";

9:    import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";

10:   import "./common/Enum.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L8

```solidity
File: scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol

4:    import "../interfaces/ERC1155TokenReceiver.sol";

5:    import "../interfaces/ERC721TokenReceiver.sol";

6:    import "../interfaces/ERC777TokensRecipient.sol";

7:    import "../interfaces/IERC165.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

7:    import "@openzeppelin/contracts/access/Ownable.sol";

8:    import "@account-abstraction/contracts/interfaces/IPaymaster.sol";

9:    import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L7

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

4:    import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

5:    import "@account-abstraction/contracts/interfaces/UserOperation.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

5:    import "../../BasePaymaster.sol";

6:    import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

7:    import "@openzeppelin/contracts/proxy/utils/Initializable.sol";

8:    import "@openzeppelin/contracts/utils/Address.sol";

9:    import "../../PaymasterHelpers.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L5

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

4:    import "./Proxy.sol";

5:    import "./BaseSmartAccount.sol"; 

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L4

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

4:    import "./libs/LibAddress.sol";

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

7:    import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";

8:    import "./BaseSmartAccount.sol";

9:    import "./common/Singleton.sol";

10:   import "./base/ModuleManager.sol";

11:   import "./base/FallbackManager.sol";

12:   import "./common/SignatureDecoder.sol";

13:   import "./common/SecuredTokenTransfer.sol";

14:   import "./interfaces/ISignatureValidator.sol";

15:   import "./interfaces/IERC165.sol";

16:   import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L4

### [N&#x2011;03]  Missing `initializer` modifier on constructor
OpenZeppelin [recommends](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5) that the `initializer` modifier be applied to constructors in order to avoid potential griefs, [social engineering](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/4), or exploits. Ensure that the modifier is applied to the implementation contract. If the default constructor is currently being used, it should be changed to be an explicit one with the modifier applied.

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

27:        Initializable,

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L27

### [N&#x2011;04]  Unused file
The file is never imported by any other file

*There are 2 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol

1:    // SPDX-License-Identifier: Apache-2.0

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol#L1

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

1:    // SPDX-License-Identifier: MIT

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L1

### [N&#x2011;05]  Adding a `return` statement when the function defines a named return variable, is redundant

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

133:              return result;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L133

### [N&#x2011;06]  `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings

*There are 3 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

28:       function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 userOpHash, uint256 maxCost)

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L28

### [N&#x2011;07]  Non-assembly method available 
`assembly{ id := chainid() }` => `uint256 id = block.chainid`, `assembly { size := extcodesize() }` => `uint256 size = address().code.length`, `assembly { hash := extcodehash() }` => `bytes32 hash = address().codehash`
There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary

*There are 2 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol

14:       assembly { csize := extcodesize(account) }

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol#L14

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

144:              id := chainid()

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L144

### [N&#x2011;08]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 192 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

/// @audit 20
213:              require(paymasterAndData.length >= 20, "AA93 invalid paymasterAndData");

/// @audit 20
214:              mUserOp.paymaster = address(bytes20(paymasterAndData[: 20]));

/// @audit 20
/// @audit 20
237:          address factory = initCode.length >= 20 ? address(bytes20(initCode[0 : 20])) : address(0);

/// @audit 3
252:          uint256 mul = mUserOp.paymaster != address(0) ? 3 : 1;

/// @audit 20
269:              address factory = address(bytes20(initCode[0 : 20]));

/// @audit 0
512:          assembly {mstore(0, number())}

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L213

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol

/// @audit 20
16:           address initAddress = address(bytes20(initCode[0 : 20]));

/// @audit 20
17:           bytes memory initCallData = initCode[20 :];

/// @audit 0
/// @audit 0x20
/// @audit 0
/// @audit 32
21:               success := call(gas(), initAddress, 0, add(initCallData, 0x20), mload(initCallData), 0, 32)

/// @audit 0
22:               sender := mload(0)

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L16

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol

/// @audit 32
65:               let len := sub(sub(sig.offset, ofs), 32)

/// @audit 0x40
66:               ret := mload(0x40)

/// @audit 0x40
/// @audit 32
67:               mstore(0x40, add(ret, add(len, 32)))

/// @audit 32
69:               calldatacopy(add(ret, 32), ofs, len)

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L65

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol

/// @audit 0x20
/// @audit 0
/// @audit 0
15:               success := call(txGas, to, value, add(data, 0x20), mload(data), 0, 0)

/// @audit 0x20
/// @audit 0
/// @audit 0
25:               success := staticcall(txGas, to, add(data, 0x20), mload(data), 0, 0)

/// @audit 0x20
/// @audit 0
/// @audit 0
35:               success := delegatecall(txGas, to, add(data, 0x20), mload(data), 0, 0)

/// @audit 0x40
42:               let ptr := mload(0x40)

/// @audit 0x40
/// @audit 0x20
43:               mstore(0x40, add(ptr, add(returndatasize(), 0x20)))

/// @audit 0x20
/// @audit 0
45:               returndatacopy(add(ptr, 0x20), 0, returndatasize())

/// @audit 32
53:               revert(add(returnData, 32), mload(returnData))

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L15

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/Executor.sol

/// @audit 0x20
/// @audit 0
/// @audit 0
23:                   success := delegatecall(txGas, to, add(data, 0x20), mload(data), 0, 0)

/// @audit 0x20
/// @audit 0
/// @audit 0
28:                   success := call(txGas, to, value, add(data, 0x20), mload(data), 0, 0)

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L23

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

/// @audit 0
/// @audit 0
38:                   return(0, 0)

/// @audit 0
/// @audit 0
40:               calldatacopy(0, 0, calldatasize())

/// @audit 0
/// @audit 0
/// @audit 20
/// @audit 0
/// @audit 0
45:               let success := call(gas(), handler, 0, 0, add(calldatasize(), 20), 0, 0)

/// @audit 0
/// @audit 0
46:               returndatacopy(0, 0, returndatasize())

/// @audit 0
48:                   revert(0, returndatasize())

/// @audit 0
50:               return(0, returndatasize())

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L38

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

/// @audit 0x40
90:               let ptr := mload(0x40)

/// @audit 0x40
/// @audit 0x20
93:               mstore(0x40, add(ptr, add(returndatasize(), 0x20)))

/// @audit 0x20
/// @audit 0
97:               returndatacopy(add(ptr, 0x20), 0, returndatasize())

/// @audit 0x0
121:          while (currentModule != address(0x0) && currentModule != SENTINEL_MODULES && moduleCount < pageSize) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L90

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol

/// @audit 0xa9059cbb
17:           bytes memory data = abi.encodeWithSelector(0xa9059cbb, receiver, amount);

/// @audit 10000
/// @audit 0
/// @audit 0x20
/// @audit 0
/// @audit 0x20
22:               let success := call(sub(gas(), 10000), token, 0, add(data, 0x20), mload(data), 0, 0x20)

/// @audit 0
31:                       transferred := 0

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L17

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol

/// @audit 0x41
24:               let signaturePos := mul(0x41, pos)

/// @audit 0x20
25:               r := mload(add(signatures, add(signaturePos, 0x20)))

/// @audit 0x40
26:               s := mload(add(signatures, add(signaturePos, 0x40)))

/// @audit 0x41
/// @audit 0xff
32:               v := and(mload(add(signatures, add(signaturePos, 0x41))), 0xff)

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol#L24

```solidity
File: scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol

/// @audit 0xf23a6e61
22:           return 0xf23a6e61;

/// @audit 0xbc197c81
32:           return 0xbc197c81;

/// @audit 0x150b7a02
41:           return 0x150b7a02;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L22

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

/// @audit 3
117:              uint256 inverse = (3 * denominator) ^ 2;

/// @audit 128
208:              if (value >> 128 > 0) {

/// @audit 128
209:                  value >>= 128;

/// @audit 128
210:                  result += 128;

/// @audit 64
212:              if (value >> 64 > 0) {

/// @audit 64
213:                  value >>= 64;

/// @audit 64
214:                  result += 64;

/// @audit 32
216:              if (value >> 32 > 0) {

/// @audit 32
217:                  value >>= 32;

/// @audit 32
218:                  result += 32;

/// @audit 16
220:              if (value >> 16 > 0) {

/// @audit 16
221:                  value >>= 16;

/// @audit 16
222:                  result += 16;

/// @audit 8
224:              if (value >> 8 > 0) {

/// @audit 8
225:                  value >>= 8;

/// @audit 8
226:                  result += 8;

/// @audit 4
228:              if (value >> 4 > 0) {

/// @audit 4
229:                  value >>= 4;

/// @audit 4
230:                  result += 4;

/// @audit 64
261:              if (value >= 10**64) {

/// @audit 64
262:                  value /= 10**64;

/// @audit 64
263:                  result += 64;

/// @audit 32
265:              if (value >= 10**32) {

/// @audit 32
266:                  value /= 10**32;

/// @audit 32
267:                  result += 32;

/// @audit 16
269:              if (value >= 10**16) {

/// @audit 16
270:                  value /= 10**16;

/// @audit 16
271:                  result += 16;

/// @audit 8
273:              if (value >= 10**8) {

/// @audit 8
274:                  value /= 10**8;

/// @audit 8
275:                  result += 8;

/// @audit 4
277:              if (value >= 10**4) {

/// @audit 4
278:                  value /= 10**4;

/// @audit 4
279:                  result += 4;

/// @audit 128
312:              if (value >> 128 > 0) {

/// @audit 128
313:                  value >>= 128;

/// @audit 16
314:                  result += 16;

/// @audit 64
316:              if (value >> 64 > 0) {

/// @audit 64
317:                  value >>= 64;

/// @audit 8
318:                  result += 8;

/// @audit 32
320:              if (value >> 32 > 0) {

/// @audit 32
321:                  value >>= 32;

/// @audit 4
322:                  result += 4;

/// @audit 16
324:              if (value >> 16 > 0) {

/// @audit 16
325:                  value >>= 16;

/// @audit 8
328:              if (value >> 8 > 0) {

/// @audit 3
342:              return result + (rounding == Rounding.Up && 1 << (result << 3) < value ? 1 : 0);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L117

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol

/// @audit 0x20
25:               let i := 0x20

/// @audit 0xf8
34:                   let operation := shr(0xf8, mload(add(transactions, i)))

/// @audit 0x60
/// @audit 0x01
37:                   let to := shr(0x60, mload(add(transactions, add(i, 0x01))))

/// @audit 0x15
39:                   let value := mload(add(transactions, add(i, 0x15)))

/// @audit 0x35
41:                   let dataLength := mload(add(transactions, add(i, 0x35)))

/// @audit 0x55
43:                   let data := add(transactions, add(i, 0x55))

/// @audit 0
44:                   let success := 0

/// @audit 0
/// @audit 0
47:                           success := call(gas(), to, value, data, dataLength, 0, 0)

/// @audit 0
46:                       case 0 {

/// @audit 0
/// @audit 0
51:                           revert(0, 0)

/// @audit 1
50:                       case 1 {

/// @audit 0
53:                   if eq(success, 0) {

/// @audit 0
/// @audit 0
54:                       revert(0, 0)

/// @audit 0x55
57:                   i := add(i, add(0x55, dataLength))

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L25

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol

/// @audit 0x20
31:               let i := 0x20

/// @audit 0xf8
40:                   let operation := shr(0xf8, mload(add(transactions, i)))

/// @audit 0x60
/// @audit 0x01
43:                   let to := shr(0x60, mload(add(transactions, add(i, 0x01))))

/// @audit 0x15
45:                   let value := mload(add(transactions, add(i, 0x15)))

/// @audit 0x35
47:                   let dataLength := mload(add(transactions, add(i, 0x35)))

/// @audit 0x55
49:                   let data := add(transactions, add(i, 0x55))

/// @audit 0
50:                   let success := 0

/// @audit 0
/// @audit 0
53:                           success := call(gas(), to, value, data, dataLength, 0, 0)

/// @audit 0
52:                       case 0 {

/// @audit 0
/// @audit 0
56:                           success := delegatecall(gas(), to, data, dataLength, 0, 0)

/// @audit 1
55:                       case 1 {

/// @audit 0
58:                   if eq(success, 0) {

/// @audit 0
/// @audit 0
59:                       revert(0, 0)

/// @audit 0x55
62:                   i := add(i, add(0x55, dataLength))

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L31

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

/// @audit 20
36:           (address paymasterId, bytes memory signature) = abi.decode(paymasterAndData[20:], (address, bytes));

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L36

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

/// @audit 64
/// @audit 65
107:          require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L107

```solidity
File: scw-contracts/contracts/smart-contract-wallet/Proxy.sol

/// @audit 0
/// @audit 0
27:               calldatacopy(0, 0, calldatasize())

/// @audit 0
/// @audit 0
/// @audit 0
28:               let result := delegatecall(gas(), target, 0, calldatasize(), 0, 0)

/// @audit 0
/// @audit 0
29:               returndatacopy(0, 0, returndatasize())

/// @audit 0
/// @audit 0
31:               case 0 {revert(0, returndatasize())}

/// @audit 0
32:               default {return (0, returndatasize())}

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L27

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

/// @audit 0x0
/// @audit 0x20
38:               proxy := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt)

/// @audit 0x0
/// @audit 0x20
57:               proxy := create(0x0, add(0x20, deploymentData), mload(deploymentData))

/// @audit 0xff
71:          bytes32 hash = keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, keccak256(code)));

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L38

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit 21000
/// @audit 8
200:          uint256 startGas = gasleft() + 21000 + msg.data.length * 8;

/// @audit 64
/// @audit 63
/// @audit 2500
/// @audit 500
224:          require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");

/// @audit 2500
229:              success = execute(_tx.to, _tx.value, _tx.data, _tx.operation, refundInfo.gasPrice == 0 ? (gasleft() - 2500) : _tx.targetTxGas);

/// @audit 65
322:                  require(uint256(s) >= uint256(1) * 65, "BSA021");

/// @audit 32
325:                  require(uint256(s) + 32 <= signatures.length, "BSA022");

/// @audit 0x20
331:                      contractSignatureLen := mload(add(add(signatures, s), 0x20))

/// @audit 32
333:                  require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");

/// @audit 0x20
340:                      contractSignature := add(add(signatures, s), 0x20)

/// @audit 30
344:          else if(v > 30) {

/// @audit 4
347:              _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);

/// @audit 0x19
/// @audit 0x01
445:          return abi.encodePacked(bytes1(0x19), bytes1(0x01), domainSeparator(), safeTxHash);

/// @audit 32
481:                  revert(add(result, 32), mload(result))

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L200

### [N&#x2011;09]  Missing event and or timelock for critical parameter change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

*There are 2 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

24        function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
25            entryPoint = _entryPoint;
26:       }

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24-L26

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

65        function setSigner( address _newVerifyingSigner) external onlyOwner{
66            require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
67            verifyingSigner = _newVerifyingSigner;
68:       }

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65-L68

### [N&#x2011;10]  Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

*There are 3 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

/// @audit setFallbackHandler()
28:           emit ChangedFallbackHandler(handler);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L28

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit updateImplementation()
124:          emit ImplementationUpdated(address(this), VERSION, _implementation);

/// @audit updateEntryPoint()
129:          emit EntryPointChanged(address(_entryPoint), _newEntryPoint);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L124

### [N&#x2011;11]  Use a more recent version of solidity
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

*There are 5 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

6:    pragma solidity ^0.8.12;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L6

```solidity
File: scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

2:    pragma solidity 0.8.12;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L2

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

2:    pragma solidity 0.8.12;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L2

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

2:    pragma solidity 0.8.12;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L2

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

2:    pragma solidity 0.8.12;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L2

### [N&#x2011;12]  Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)
While the compiler knows to optimize away the exponentiation, it's still better coding practice to use idioms that do not require compiler optimization, if they exist

*There are 13 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

261:              if (value >= 10**64) {

262:                  value /= 10**64;

265:              if (value >= 10**32) {

266:                  value /= 10**32;

269:              if (value >= 10**16) {

270:                  value /= 10**16;

273:              if (value >= 10**8) {

274:                  value /= 10**8;

277:              if (value >= 10**4) {

278:                  value /= 10**4;

281:              if (value >= 10**2) {

282:                  value /= 10**2;

285:              if (value >= 10**1) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L261

### [N&#x2011;13]  Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

*There are 2 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

/// @audit seen in scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol 
11:       string public constant VERSION = "1.0.2";

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L11

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit seen in scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol 
36:       string public constant VERSION = "1.0.2"; // using AA 0.3.0

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L36

### [N&#x2011;14]  Inconsistent spacing in comments
Some lines use `// x` and some use `//x`. The instances below point out the usages that don't follow the majority, within each file

*There are 30 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

85:       } //unchecked

144:      //a memory copy of UserOp fields (except that dynamic byte arrays: callData, initCode and signature

187:          //note: opIndex is ignored (relevant only if mode==postOpReverted, which is only possible outside of innerHandleOp)

250:          //when using a Paymaster, the verificationGasLimit is used also to as a limit for the postOp call.

402:          //a "marker" where account opcode validation is done and paymaster opcode validation is about to start

489:              //legacy mode (for networks that don't support basefee opcode)

508:      //place the NUMBER opcode in the code.

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L85

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol

70:       //UserOps handled, per aggregator

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L70

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol

45:           postOpReverted //user op succeeded, but caused postOp to revert. Now its a 2nd call, after user's op was deliberately reverted.

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L45

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol

61:       //API struct used by getStakeInfo and simulateValidation

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L61

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol

38:           //read sender from userOp, which is first userOp member (saves 800 gas...)

43:       //relayer/block builder might submit the TX with higher priorityFee, but the user should not

50:               //legacy mode (for networks that don't support basefee opcode)

58:           //lighter signature scheme. must match UserOp.ts#packUserOp

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L38

```solidity
File: scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

22:           uint256 gasPrice; //gasPrice or tokenGasPrice

110:              //ignore failure (its EntryPoint's job to verify, not account.)

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L22

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

79:           //can't use userOp.hash(), since it contains also the paymasterAndData itself.

105:          //ECDSA library supports both 64 and 65-byte long signatures.

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L79

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

13:       //states : registry

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L13

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

149:      //@review getNonce specific to EntryPoint requirements

201:          //console.log("init %s", 21000 + msg.data.length * 8);

237:                  //console.log("sent %s", startGas - gasleft());

243:              //console.log("extra gas %s ", extraGas);

268:          //console.log("hp %s", requiredGas);

292:          //console.log("hpr %s", requiredGas);

313:          //review

486:      //called by entryPoint, only after validateUserOp succeeded.

487:      //@review

488:      //Method is updated to instruct delegate call and emit regular events

509:          //ignore signature mismatch of from==ZERO_ADDRESS (for eth_callUserOp validation purposes)

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L149

### [N&#x2011;15]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 12 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

319:          try IAccount(sender).validateUserOp{gas : mUserOp.verificationGasLimit}(op, opInfo.userOpHash, aggregator, missingAccountFunds) returns (uint256 _deadline) {

349:      function _validatePaymasterPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, uint256 requiredPreFund, uint256 gasUsedByValidateAccountPrepayment) internal returns (bytes memory context, uint256 deadline) {

440:      function _handlePostOp(uint256 opIndex, IPaymaster.PostOpMode mode, UserOpInfo memory opInfo, bytes memory context, uint256 actualGas) private returns (uint256 actualGasCost) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L319

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol

28:       event UserOperationEvent(bytes32 indexed userOpHash, address indexed sender, address indexed paymaster, uint256 nonce, bool success, uint256 actualGasCost, uint256 actualGasUsed);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L28

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol

16:        * @param paymasterAndData if set, this field hold the paymaster address and "paymaster-specific-data". the paymaster will pay for the transaction instead of the sender

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L16

```solidity
File: scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol

10:           @dev An ERC1155-compliant smart contract MUST call this function on the token recipient contract, at the end of a `safeTransferFrom` after the balance has been updated.        

31:           @dev An ERC1155-compliant smart contract MUST call this function on the token recipient contract, at the end of a `safeBatchTransferFrom` after the balances have been updated.        

32:           This function MUST return `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))` (i.e. 0xbc197c81) if it accepts the transfer(s).

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L10

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

46:       //     "AccountTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"

239:                  payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);

357:      ///      Since the `estimateGas` function includes refunds, call this method to get an estimated of the costs that are deducted from the safe with `execTransaction`

489:      function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L46

### [N&#x2011;16]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 2 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

6:    pragma solidity ^0.8.12;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L6

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol

2:    pragma solidity ^0.8.12;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L2

### [N&#x2011;17]  Typos

*There are 3 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol

/// @audit valiate
10:    * - the validateUserOp MUST valiate the aggregator parameter, and MAY ignore the userOp.signature field.

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L10

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol

/// @audit keccack
15:           // 0xa9059cbb - keccack("transfer(address,uint256)")

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L15

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit Seperators
38:       // Domain Seperators

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L38

### [N&#x2011;18]  Misplaced SPDX identifier
The SPDX identifier should be on the very first line of the file

*There are 2 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

1:    /**

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L1

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol

1:    /**

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L1

### [N&#x2011;19]  File is missing NatSpec

*There are 7 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol


```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol


```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/Executor.sol


```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/Enum.sol


```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Enum.sol

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol


```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol

```solidity
File: scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol


```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol

```solidity
File: scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol


```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol

### [N&#x2011;20]  NatSpec is incomplete

*There are 17 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

/// @audit Missing: '@param outOpInfo'
/// @audit Missing: '@param aggregator'
/// @audit Missing: '@return'
378       /**
379        * validate account and paymaster (if defined).
380        * also make sure total validation doesn't exceed verificationGasLimit
381        * this method is called off-chain (simulateValidation()) and on-chain (from handleOps)
382        * @param opIndex the index of this userOp into the "opInfos" array
383        * @param userOp the userOp to validate
384        */
385       function _validatePrepayment(uint256 opIndex, UserOperation calldata userOp, UserOpInfo memory outOpInfo, address aggregator)
386:      private returns (address actualAggregator, uint256 deadline) {

/// @audit Missing: '@return'
438        * @param actualGas the gas used so far by this user operation
439        */
440:      function _handlePostOp(uint256 opIndex, IPaymaster.PostOpMode mode, UserOpInfo memory opInfo, bytes memory context, uint256 actualGas) private returns (uint256 actualGasCost) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L378-L386

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

/// @audit Missing: '@return'
59        /// @param data Data payload of module transaction.
60        /// @param operation Operation type of module transaction.
61        function execTransactionFromModule(
62            address to,
63            uint256 value,
64            bytes memory data,
65            Enum.Operation operation
66:       ) public virtual returns (bool success) {

/// @audit Missing: '@return'
78        /// @param data Data payload of module transaction.
79        /// @param operation Operation type of module transaction.
80        function execTransactionFromModuleReturnData(
81            address to,
82            uint256 value,
83            bytes memory data,
84            Enum.Operation operation
85:       ) public returns (bool success, bytes memory returnData) {

/// @audit Missing: '@param module'
103       /// @dev Returns if an module is enabled
104       /// @return True if the module is enabled
105:      function isModuleEnabled(address module) public view returns (bool) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L59-L66

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol

/// @audit Missing: '@return'
8         /// @param receiver Receiver to whom the token should be transferred
9         /// @param amount The amount of tokens that should be transferred
10        function transferToken(
11            address token,
12            address receiver,
13            uint256 amount
14:       ) internal returns (bool transferred) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L8-L14

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol

/// @audit Missing: '@return'
8         /// @param pos which signature to read. A prior bounds check of this parameter should be performed, to avoid out of bounds access
9         /// @param signatures concatenated rsv signatures
10        function signatureSplit(bytes memory signatures, uint256 pos)
11            internal
12            pure
13            returns (
14                uint8 v,
15                bytes32 r,
16:               bytes32 s

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol#L8-L16

```solidity
File: scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol

/// @audit Missing: '@return'
17         * MUST allow external calls
18         */
19:       function isValidSignature(bytes memory _data, bytes memory _signature) public view virtual returns (bytes4);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol#L17-L19

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol

/// @audit Missing: '@return'
9        *      a contract's deployment, as the code is not yet stored on-chain.
10       */
11:     function isContract(address account) internal view returns (bool) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol#L9-L11

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

/// @audit Missing: '@return'
31         * @param _index extra salt that allows to deploy more wallets if needed for same EOA (default 0)
32         */
33:       function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address proxy){

/// @audit Missing: '@return'
51         * @param _handler fallback handler address
52        */ 
53:       function deployWallet(address _owner, address _entryPoint, address _handler) public returns(address proxy){ 

/// @audit Missing: '@return'
66         * @param _index extra salt that allows to deploy more wallets if needed for same EOA (default 0)
67        */
68:       function getAddressForCounterfactualWallet(address _owner, uint _index) external view returns (address _wallet) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L31-L33

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit Missing: '@return'
190       /// @param refundInfo Required information for gas refunds
191       /// @param signatures Packed signature data ({bytes32 r}{bytes32 s}{uint8 v})
192       function execTransaction(
193           Transaction memory _tx,
194           uint256 batchId,
195           FeeRefund memory refundInfo,
196           bytes memory signatures
197:      ) public payable virtual override returns (bool success) {

/// @audit Missing: '@param data'
297       /**
298        * @dev Checks whether the signature provided is valid for the provided data, hash. Will revert otherwise.
299        * @param dataHash Hash of the data (could be either a message hash or transaction hash)
300        * @param signatures Signature data that should be verified. Can be ECDSA signature, contract signature (EIP-1271) or approved hash.
301        */
302       function checkSignatures(
303           bytes32 dataHash,
304           bytes memory data,
305:          bytes memory signatures

/// @audit Missing: '@param tokenGasPriceFactor'
377       /// @dev Returns hash to be signed by owner.
378       /// @param to Destination address.
379       /// @param value Ether value.
380       /// @param data Data payload.
381       /// @param operation Operation type.
382       /// @param targetTxGas Fas that should be used for the safe transaction.
383       /// @param baseGas Gas costs for data used to trigger the safe transaction.
384       /// @param gasPrice Maximum gas price that should be used for this transaction.
385       /// @param gasToken Token address (or 0 if ETH) that is used for the payment.
386       /// @param refundReceiver Address of receiver of gas payment (or 0 if tx.origin).
387       /// @param _nonce Transaction nonce.
388       /// @return Transaction hash.
389       function getTransactionHash(
390           address to,
391           uint256 value,
392           bytes calldata data,
393           Enum.Operation operation,
394           uint256 targetTxGas,
395           uint256 baseGas,
396           uint256 gasPrice,
397           uint256 tokenGasPriceFactor,
398           address gasToken,
399           address payable refundReceiver,
400           uint256 _nonce
401:      ) public view returns (bytes32) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L190-L197

### [N&#x2011;21]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 5 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

/// @audit info
18:       function getDepositInfo(address account) public view returns (DepositInfo memory info) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L18

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

/// @audit context
24        function paymasterContext(
25            UserOperation calldata op,
26            PaymasterData memory data
27:       ) internal pure returns (bytes memory context) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L24-L27

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

/// @audit context
/// @audit deadline
97        function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 /*userOpHash*/, uint256 requiredPreFund)
98:       external view override returns (bytes memory context, uint256 deadline) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L97-L98

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit deadline
506       function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash, address)
507:      internal override virtual returns (uint256 deadline) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L506-L507

### [N&#x2011;22]  Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions

*There are 5 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

49:           require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

286:              require(success, "BSA011");

289:              require(transferToken(gasToken, receiver, payment), "BSA012");

374:          revert(string(abi.encodePacked(requiredGas)));

351:              require(_signer == owner, "INVALID_SIGNATURE");

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L286

### [N&#x2011;23]  Consider using `delete` rather than assigning zero to clear values
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic

*There are 3 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

102:          info.unstakeDelaySec = 0;

103:          info.withdrawTime = 0;

104:          info.stake = 0;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L102

### [N&#x2011;24]  Avoid the use of sensitive terms
Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist

*There are 87 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

447:          address paymaster = mUserOp.paymaster;

211:          bytes calldata paymasterAndData = userOp.paymasterAndData;

357:          DepositInfo storage paymasterInfo = deposits[paymaster];

408:              uint paymasterDeadline;

349:      function _validatePaymasterPrepayment(uint256 opIndex, UserOperation calldata op, UserOpInfo memory opInfo, uint256 requiredPreFund, uint256 gasUsedByValidateAccountPrepayment) internal returns (bytes memory context, uint256 deadline) {

221:       * Simulate a call to account.validateUserOp and paymaster.validatePaymasterUserOp.

250:          //when using a Paymaster, the verificationGasLimit is used also to as a limit for the postOp call.

343:       * in case the request has a paymaster:

344:       * validate paymaster is staked and has enough deposit.

345:       * call paymaster.validatePaymasterUserOp.

346:       * revert with proper FailedOp in case paymaster reverts.

347:       * decrement paymaster's deposit

379:       * validate account and paymaster (if defined).

402:          //a "marker" where account opcode validation is done and paymaster opcode validation is about to start

432:       * if a paymaster is defined and its validation returned a non-empty context, its postOp is called.

433:       * the excess amount is refunded to the account (or paymaster - if it is was used in the request)

437:       * @param context the context returned in validatePaymasterUserOp

510:      // account and paymaster.

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L447

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

10:    * deposit is just a balance used to pay for UserOperations (either by a paymaster or an account)

11:    * stake is value locked for at least "unstakeDelay" by a paymaster.

15:       /// maps paymaster to their deposits and stakes

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L10

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol

20:        *      In case there is a paymaster in the request (or the current deposit is high enough), this value will be zero.

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L20

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol

63:       error FailedOp(uint256 opIndex, address paymaster, string reason);

139:          StakeInfo senderInfo, StakeInfo factoryInfo, StakeInfo paymasterInfo, AggregatorStakeInfo aggregatorInfo);

22:        * @param paymaster - if non-null, the paymaster that pays for this request.

24:        * @param actualGasCost - actual amount paid (by account or paymaster) for this UserOperation

35:        * @param paymaster the paymaster used by this UserOp

57:        *  @param paymaster - if paymaster.validatePaymasterUserOp fails, this will be the paymaster's address. if validateUserOp failed,

58:        *       this value will be zero (since it failed before accessing the paymaster)

61:        *   Useful for mitigating DoS attempts against batchers or for troubleshooting of account/paymaster reverts.

107:       * Simulate a call to account.validateUserOp and paymaster.validatePaymasterUserOp.

118:       * @param deadline until what time this userOp is valid (the minimum value of account and paymaster's deadline)

121:       * @param paymasterInfo stake information about the paymaster (if any)

131:       * @param deadline until what time this userOp is valid (the minimum value of account and paymaster's deadline)

134:       * @param paymasterInfo stake information about the paymaster (if any)

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L63

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol

26        function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 userOpHash, uint256 maxCost)
27:       external returns (bytes memory context, uint256 deadline);

7:     * the interface exposed by a paymaster contract, who agrees to pay the gas for user's operations.

13:        * payment validation: check if paymaster agree to pay.

16:        * Note that bundlers will reject this method if it changes the state, unless the paymaster is trusted (whitelisted)

17:        * The paymaster pre-pays using its deposit, and receive back a refund after the postOp method returns.

37:        * @param context - the context value returned by validatePaymasterUserOp

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L26-L27

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol

6:     * deposit is just a balance used to pay for UserOperations (either by a paymaster or an account)

43:        * @param staked true if this account is staked as a paymaster

44:        * @param stake actual amount of ether staked for this paymaster.

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L6

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol

30:           bytes paymasterAndData;

12:        * @param verificationGasLimit gas used for validateUserOp and validatePaymasterUserOp

16:        * @param paymasterAndData if set, this field hold the paymaster address and "paymaster-specific-data". the paymaster will pay for the transaction instead of the sender

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L30

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

28:       /// @dev Allows to add a module to the whitelist.

31:       /// @param module Module to be whitelisted.

42:       /// @dev Allows to remove a module from the whitelist.

67:           // Only whitelisted modules are allowed.

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L28

```solidity
File: scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

104:       *  this value MAY be zero, in case there is enough deposit, or the userOp has a paymaster.

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L104

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

28        function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 userOpHash, uint256 maxCost)
29:       external virtual override returns (bytes memory context, uint256 deadline);

12:    * Helper class for creating a paymaster.

39:        * @dev if subclass returns a non-empty context from validatePaymasterUserOp, it must also implement this method.

45:        * @param context - the context value returned by validatePaymasterUserOp

51:           // subclass must override this method if validatePaymasterUserOp returns a context

56:        * add a deposit for this paymaster, used for paying for transaction fees

71:        * add stake for this paymaster.

73:        * @param unstakeDelaySec - the unstake delay for this paymaster. Can only be increased.

80:        * return current paymaster's deposit on the entryPoint.

88:        * The paymaster can't serve requests once unlocked, until it calls addStake again

95:        * withdraw the entire paymaster's stake.

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L28-L29

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol

44:           (address paymasterId) = abi.decode(context, (address));

35:           bytes calldata paymasterAndData = op.paymasterAndData;

24        function paymasterContext(
25            UserOperation calldata op,
26            PaymasterData memory data
27:       ) internal pure returns (bytes memory context) {

34:       function decodePaymasterData(UserOperation calldata op) internal pure returns (PaymasterData memory) {

43:       function decodePaymasterContext(bytes memory context) internal pure returns (PaymasterContext memory) {

22:        * @dev Encodes the paymaster context: sender, token, rate, and fee

32:        * @dev Decodes paymaster data assuming it follows PaymasterData

41:        * @dev Decodes paymaster context assuming it follows PaymasterContext

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L44

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

31:       mapping(address => uint256) public paymasterIdBalances;

48:       function depositFor(address paymasterId) public payable {

102:          PaymasterData memory paymasterData = userOp.decodePaymasterData();

127:      address extractedPaymasterId = data.paymasterId;

97        function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 /*userOpHash*/, uint256 requiredPreFund)
98:       external view override returns (bytes memory context, uint256 deadline) {

14:    * A sample paymaster that uses external service to decide whether to pay for the UserOp.

15:    * The paymaster trusts an external signer to sign the transaction.

19:    * - the paymaster signs to agree to PAY for GAS.

46:        * add a deposit for this paymaster and given paymasterId (Dapp Depositor address), used for paying for transaction fees

73:        * it is called on-chain from the validatePaymasterUserOp, to validate the signature.

74:        * note that this signature covers all fields of the UserOperation, except the "paymasterAndData",

79:           //can't use userOp.hash(), since it contains also the paymasterAndData itself.

95:        * the "paymasterAndData" is expected to be the paymaster and a signature over the entire request params

106:          // we only "require" it here so that the revert reason on invalid signature will be of "VerifyingPaymaster", and not "ECDSA"

114:     * @dev Executes the paymaster's payment conditions

116:     * @param context payment conditions signed by the paymaster in `validatePaymasterUserOp`

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L31

### [N&#x2011;25]  Large assembly blocks should have extensive comments
Assembly blocks are take a lot more time to audit than normal Solidity code, and often have gotchas and side-effects that the Solidity versions of the same code do not. Consider adding more comments explaining what is being done in every step of the assembly code

*There are 5 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol

63            assembly {
64                let ofs := userOp
65                let len := sub(sub(sig.offset, ofs), 32)
66                ret := mload(0x40)
67                mstore(0x40, add(ret, add(len, 32)))
68                mstore(ret, len)
69                calldatacopy(add(ret, 32), ofs, len)
70:           }

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L63-L70

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol

41            assembly {
42                let ptr := mload(0x40)
43                mstore(0x40, add(ptr, add(returndatasize(), 0x20)))
44                mstore(ptr, returndatasize())
45                returndatacopy(add(ptr, 0x20), 0, returndatasize())
46                returnData := ptr
47:           }

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L41-L47

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

35            assembly {
36                let handler := sload(slot)
37                if iszero(handler) {
38                    return(0, 0)
39                }
40                calldatacopy(0, 0, calldatasize())
41                // The msg.sender address is shifted to the left by 12 bytes to remove the padding
42                // Then the address without padding is stored right after the calldata
43                mstore(calldatasize(), shl(96, caller()))
44                // Add 20 bytes for the address appended add the end
45                let success := call(gas(), handler, 0, 0, add(calldatasize(), 20), 0, 0)
46                returndatacopy(0, 0, returndatasize())
47                if iszero(success) {
48                    revert(0, returndatasize())
49                }
50                return(0, returndatasize())
51:           }

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L35-L51

```solidity
File: scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol

19            assembly {
20                // We write the return value to scratch space.
21                // See https://docs.soliditylang.org/en/v0.7.6/internals/layout_in_memory.html#layout-in-memory
22                let success := call(sub(gas(), 10000), token, 0, add(data, 0x20), mload(data), 0, 0x20)
23                switch returndatasize()
24                    case 0 {
25                        transferred := success
26                    }
27                    case 0x20 {
28                        transferred := iszero(or(iszero(success), iszero(mload(0))))
29                    }
30                    default {
31                        transferred := 0
32                    }
33:           }

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L19-L33

```solidity
File: scw-contracts/contracts/smart-contract-wallet/Proxy.sol

25            assembly {
26                target := sload(_IMPLEMENTATION_SLOT)
27                calldatacopy(0, 0, calldatasize())
28                let result := delegatecall(gas(), target, 0, calldatasize(), 0, 0)
29                returndatacopy(0, 0, returndatasize())
30                switch result
31                case 0 {revert(0, returndatasize())}
32                default {return (0, returndatasize())}
33:           }

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L25-L33

### [N&#x2011;26]  Contracts should have full test coverage
While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;27]  Large or complicated code bases should implement fuzzing tests
Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement [fuzzing tests](https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05). Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;28]  Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*There are 31 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

/// @audit _executeUserOp() came earlier
68:       function handleOps(UserOperation[] calldata ops, address payable beneficiary) public {

/// @audit handleAggregatedOps() came earlier
168:      function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost) {

/// @audit _copyUserOpToMemory() came earlier
226:      function simulateValidation(UserOperation calldata userOp) external {

/// @audit _createSenderIfNeeded() came earlier
280:      function getSenderAddress(bytes calldata initCode) public {

/// @audit _handlePostOp() came earlier
484:      function getUserOpGasPrice(MemoryUserOp memory mUserOp) internal view returns (uint256) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L68

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol

/// @audit getStakeInfo() came earlier
30:       function balanceOf(address account) public view returns (uint256) {

/// @audit balanceOf() came earlier
34        receive() external payable {
35:           depositTo(msg.sender);

/// @audit internalIncrementDeposit() came earlier
48:       function depositTo(address account) public payable {

/// @audit addStake() came earlier
80        function unlockStake() external {
81:           DepositInfo storage info = deposits[msg.sender];

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L30

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

/// @audit internalSetFallbackHandler() came earlier
26:       function setFallbackHandler(address handler) public authorized {

/// @audit setFallbackHandler() came earlier
32        fallback() external {
33:           bytes32 slot = FALLBACK_HANDLER_STORAGE_SLOT;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L26

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

/// @audit setupModules() came earlier
32:       function enableModule(address module) public authorized {

/// @audit isModuleEnabled() came earlier
114:      function getModulesPaginated(address start, uint256 pageSize) external view returns (address[] memory array, address next) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L32

```solidity
File: scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol

/// @audit entryPoint() came earlier
60        function validateUserOp(UserOperation calldata userOp, bytes32 userOpHash, address aggregator, uint256 missingAccountFunds)
61:       external override virtual returns (uint256 deadline) {

/// @audit _payPrefund() came earlier
114:      function init(address _owner, address _entryPointAddress, address _handler) external virtual;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L60-L61

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

/// @audit setEntryPoint() came earlier
28        function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 userOpHash, uint256 maxCost)
29:       external virtual override returns (bytes memory context, uint256 deadline);

/// @audit _postOp() came earlier
58        function deposit() public virtual payable {
59:           entryPoint.depositTo{value : msg.value}(address(this));

/// @audit withdrawTo() came earlier
75:       function addStake(uint32 unstakeDelaySec) external payable onlyOwner {

/// @audit getDeposit() came earlier
90:       function unlockStake() external onlyOwner {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L28-L29

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

/// @audit withdrawTo() came earlier
65:       function setSigner( address _newVerifyingSigner) external onlyOwner{

/// @audit getHash() came earlier
97        function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 /*userOpHash*/, uint256 requiredPreFund)
98:       external view override returns (bytes memory context, uint256 deadline) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

/// @audit deployWallet() came earlier
68:       function getAddressForCounterfactualWallet(address _owner, uint _index) external view returns (address _wallet) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L68

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit entryPoint() came earlier
109:      function setOwner(address _newOwner) external mixedAuth {

/// @audit max() came earlier
192       function execTransaction(
193           Transaction memory _tx,
194           uint256 batchId,
195           FeeRefund memory refundInfo,
196           bytes memory signatures
197:      ) public payable virtual override returns (bool success) {

/// @audit handlePayment() came earlier
271       function handlePaymentRevert(
272           uint256 gasUsed,
273           uint256 baseGas,
274           uint256 gasPrice,
275           uint256 tokenGasPriceFactor,
276           address gasToken,
277           address payable refundReceiver
278:      ) external returns (uint256 payment) {

/// @audit checkSignatures() came earlier
363       function requiredTxGas(
364           address to,
365           uint256 value,
366           bytes calldata data,
367           Enum.Operation operation
368:      ) external returns (uint256) {

/// @audit encodeTransactionData() came earlier
449:      function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {

/// @audit _call() came earlier
489:      function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        

/// @audit _validateSignature() came earlier
518:      function getDeposit() public view returns (uint256) {

/// @audit withdrawDepositTo() came earlier
545:      function supportsInterface(bytes4 interfaceId) external view virtual override returns (bool) {

/// @audit supportsInterface() came earlier
550:      receive() external payable {}

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109

### [N&#x2011;29]  Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Modifiers, and 5) Functions, but the contract(s) below do not follow this ordering

*There are 3 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol

/// @audit function postOp came earlier
42        enum PostOpMode {
43            opSucceeded, // user op succeeded
44            opReverted, // user op reverted. still has to pay for gas.
45            postOpReverted //user op succeeded, but caused postOp to revert. Now its a 2nd call, after user's op was deliberately reverted.
46:       }

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L42-L46

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

/// @audit event ChangedFallbackHandler came earlier
12:       bytes32 internal constant FALLBACK_HANDLER_STORAGE_SLOT = 0x6c9a6c4a39284e37ed1cf53d337577d14212a4870fb976a4366c693b939918d5;

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L12

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

/// @audit event ExecutionFromModuleFailure came earlier
16:       address internal constant SENTINEL_MODULES = address(0x1);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L16

### [N&#x2011;30]  Interfaces should be indicated with an `I` prefix in the contract name

*There are 3 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol

7:    interface ERC1155TokenReceiver {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol#L7

```solidity
File: scw-contracts/contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol

5:    interface ERC721TokenReceiver {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol#L5

```solidity
File: scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol

4:    interface ERC777TokensRecipient {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol#L4


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 1 | 

Total: 1 instances over 1 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `require()`/`revert()` statements should have descriptive reason strings | 4 | 
| [N&#x2011;02] | `public` functions not called by the contract should be declared `external` instead | 18 | 
| [N&#x2011;03] | `constant`s should be defined rather than using magic numbers | 1 | 
| [N&#x2011;04] | Event is missing `indexed` fields | 17 | 

Total: 40 instances over 4 issues





## Low Risk Issues

### [L&#x2011;01]  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).

If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit (valid but excluded finding)
347:              _signer = ecrecover(keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", dataHash)), v - 4, r, s);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L347

## Non-critical Issues

### [N&#x2011;01]  `require()`/`revert()` statements should have descriptive reason strings

*There are 4 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/Math.sol

/// @audit (valid but excluded finding)
78:               require(denominator > prod1);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L78

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol

/// @audit (valid but excluded finding)
105:          require(msg.sender == address(entryPoint));

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L105

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit (valid but excluded finding)
371:          require(execute(to, value, data, operation, gasleft()));

/// @audit (valid but excluded finding)
528:          require(req);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L371

### [N&#x2011;02]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 18 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

/// @audit (valid but excluded finding)
68:       function handleOps(UserOperation[] calldata ops, address payable beneficiary) public {

/// @audit (valid but excluded finding)
93        function handleAggregatedOps(
94            UserOpsPerAggregator[] calldata opsPerAggregator,
95:           address payable beneficiary

/// @audit (valid but excluded finding)
280:      function getSenderAddress(bytes calldata initCode) public {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L68

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

/// @audit (valid but excluded finding)
26:       function setFallbackHandler(address handler) public authorized {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L26

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

/// @audit (valid but excluded finding)
32:       function enableModule(address module) public authorized {

/// @audit (valid but excluded finding)
47:       function disableModule(address prevModule, address module) public authorized {

/// @audit (valid but excluded finding)
80        function execTransactionFromModuleReturnData(
81            address to,
82            uint256 value,
83            bytes memory data,
84            Enum.Operation operation
85:       ) public returns (bool success, bytes memory returnData) {

/// @audit (valid but excluded finding)
105:      function isModuleEnabled(address module) public view returns (bool) {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L32

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol

/// @audit (valid but excluded finding)
21:       function multiSend(bytes memory transactions) public payable {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L21

```solidity
File: scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol

/// @audit (valid but excluded finding)
26:       function multiSend(bytes memory transactions) public payable {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L26

```solidity
File: scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

/// @audit (valid but excluded finding)
48:       function depositFor(address paymasterId) public payable {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L48

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol

/// @audit (valid but excluded finding)
33:       function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address proxy){

/// @audit (valid but excluded finding)
53:       function deployWallet(address _owner, address _entryPoint, address _handler) public returns(address proxy){ 

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L33

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit (valid but excluded finding)
155       function getNonce(uint256 batchId)
156       public view
157:      returns (uint256) {

/// @audit (valid but excluded finding)
389       function getTransactionHash(
390           address to,
391           uint256 value,
392           bytes calldata data,
393           Enum.Operation operation,
394           uint256 targetTxGas,
395           uint256 baseGas,
396           uint256 gasPrice,
397           uint256 tokenGasPriceFactor,
398           address gasToken,
399           address payable refundReceiver,
400           uint256 _nonce
401:      ) public view returns (bytes32) {

/// @audit (valid but excluded finding)
518:      function getDeposit() public view returns (uint256) {

/// @audit (valid but excluded finding)
525       function addDeposit() public payable {
526   
527:          (bool req,) = address(entryPoint()).call{value : msg.value}("");

/// @audit (valid but excluded finding)
536:      function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L155-L157

### [N&#x2011;03]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There is 1 instance of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

/// @audit 96 - (valid but excluded finding)
43:               mstore(calldatasize(), shl(96, caller()))

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L43

### [N&#x2011;04]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 17 instances of this issue:*

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol

/// @audit (valid but excluded finding)
37:       event AccountDeployed(bytes32 indexed userOpHash, address indexed sender, address factory, address paymaster);

/// @audit (valid but excluded finding)
46:       event UserOperationRevertReason(bytes32 indexed userOpHash, address indexed sender, uint256 nonce, bytes revertReason);

/// @audit (valid but excluded finding)
51:       event SignatureAggregatorChanged(address aggregator);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L37

```solidity
File: scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol

/// @audit (valid but excluded finding)
11        event Deposited(
12            address indexed account,
13            uint256 totalDeposit
14:       );

/// @audit (valid but excluded finding)
16        event Withdrawn(
17            address indexed account,
18            address withdrawAddress,
19            uint256 amount
20:       );

/// @audit (valid but excluded finding)
23        event StakeLocked(
24            address indexed account,
25            uint256 totalStaked,
26            uint256 withdrawTime
27:       );

/// @audit (valid but excluded finding)
30        event StakeUnlocked(
31            address indexed account,
32            uint256 withdrawTime
33:       );

/// @audit (valid but excluded finding)
35        event StakeWithdrawn(
36            address indexed account,
37            address withdrawAddress,
38            uint256 amount
39:       );

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L11-L14

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/Executor.sol

/// @audit (valid but excluded finding)
9:        event ExecutionFailure(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);

/// @audit (valid but excluded finding)
10:       event ExecutionSuccess(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L9

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol

/// @audit (valid but excluded finding)
9:        event ChangedFallbackHandler(address handler);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L9

```solidity
File: scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol

/// @audit (valid but excluded finding)
11:       event EnabledModule(address module);

/// @audit (valid but excluded finding)
12:       event DisabledModule(address module);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L11

```solidity
File: scw-contracts/contracts/smart-contract-wallet/Proxy.sol

/// @audit (valid but excluded finding)
13:       event Received(uint indexed value, address indexed sender, bytes data);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L13

```solidity
File: scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

/// @audit (valid but excluded finding)
64:       event ImplementationUpdated(address _scw, string version, address newImplementation);

/// @audit (valid but excluded finding)
65:       event EntryPointChanged(address oldEntryPoint, address newEntryPoint);

/// @audit (valid but excluded finding)
67:       event WalletHandlePayment(bytes32 txHash, uint256 payment);

```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L64
