
### 01  REPLACE INLINE ASSEMBLY WITH ACCOUNT.CODE.LENGTH

*Number of Instances Identified: 1*

`<address>.code.length` can be used in Solidity >= 0.8.0 to access an account’s code size and check if it is a contract without inline assembly.

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol

```
14: assembly { csize := extcodesize(account)
```


### 02 AVOID USING TX.ORIGIN

*Number of Instances Identified: 3*

`tx.origin` is a global variable in Solidity that returns the address of the account that sent the transaction.

Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.

This can make it easier to create a vault on behalf of another user with an external administrator (by receiving it as an argument).

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol

```
257: address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
281: address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
511: require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
```


### 03 REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

*Number of Instances Identified: 2*

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol

```
16: assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol

```
13: assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```


### 04 FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES

All the contracts in scope should update the import usage.

### Description

Our Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

### Recommendation

`import {contract1 , contract2} from "filename.sol";`


### 05 MISSING EVENTS FOR FUNCTIONS THAT CHANGE CRITICAL PARAMETERS

he functions that change critical parameters should emit events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

Missing events and timelocks do not promote transparency and if such changes immediately affect users’ perception of fairness or trustworthiness, they could exit the protocol causing a reduction in liquidity which could negatively impact protocol TVL and reputation.

### Proof of Concept

*Number of Instances Identified: 2*

Navigate to the following contracts:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24-L26

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65-L68

### 06 UPGRADEABLE CONTRACT IS MISSING A `__GAP[50]` STORAGE VARIABLE TO ALLOW FOR NEW STORAGE VARIABLES IN LATER VERSIONS

*Number of Instances Identified: 1*

See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```
18-28 contract SmartAccount is 
     Singleton,
     BaseSmartAccount,
     IERC165,
     ModuleManager,
     SignatureDecoder,
     SecuredTokenTransfer,
     ISignatureValidatorConstants,
     FallbackManager,
     Initializable,
     ReentrancyGuardUpgradeable
```


### 07 INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS

All the contracts in scope should update the import usage.

### Description

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

[https://docs.soliditylang.org/en/v0.8.15/natspec-format.html](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

If Return parameters are declared, you must prefix them with `/// @return`.

Some code analysis programs do analysis by reading NatSpec details, if they can’t see the `@return` tag, they do incomplete analysis.

### Recommendation

Include return parameters in NatSpec comments

Recommendation Code Style:

```
   /// @notice information about what a function does
   /// @param pageId The id of the page to get the URI for.
   /// @return Returns a page's URI if it has been minted 
```


### 08 USE LATEST SOLIDITY VERSION

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives [security fixes](https://github.com/ethereum/solidity/security/policy#supported-versions). Furthermore, breaking changes as well as new features are introduced regularly.
Latest Version is 0.8.17

### 09 TYPOS

*Number of Instances Identified: 1*

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol

It should be validate instead of valiate

```
10: the validateUserOp MUST valiate the aggregator parameter, and MAY ignore the userOp.signature field.
```

### 10 OPEN TODOS

*Number of Instances Identified: 1*

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol

```
255: // TODO: copy logic of gasPrice?
```

