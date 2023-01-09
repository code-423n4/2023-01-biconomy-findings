- [Low](#low)
    - [**1. Use abstract for base contracts**](#1-use-abstract-for-base-contracts)
    - [**2. Mixing and Outdated compiler**](#2-mixing-and-outdated-compiler)
    - [**3. Wrong logic in paymasterContext**](#3-wrong-logic-in-paymastercontext)
    - [**4. Lack of checks**](#4-lack-of-checks)
    - [**5. Centralization risks**](#5-centralization-risks)
    - [**6. Poxy isues**](#6-poxy-isues)
    - [**7. Integer overflow**](#7-integer-overflow)
    - [**8. Loss of tokens**](#8-loss-of-tokens)
- [Non critical](#non-critical)
    - [**9. Open TODO**](#9-open-todo)
    - [**10. Wrong comment**](#10-wrong-comment)
    - [**11. Lack of ACK during owner change**](#11-lack-of-ack-during-owner-change)
    - [**12. Unsafe low level call**](#12-unsafe-low-level-call)
    - [**13. Wong naming**](#13-wong-naming)
    - [**14. Avoid hardcoded values**](#14-avoid-hardcoded-values)

# Low

## **1. Use `abstract` for base contracts**

`abstract` contracts are contracts that have at least one function without its implementation. **An instance of an abstract cannot be created.**

**Reference:**

- https://docs.soliditylang.org/en/v0.6.2/contracts.html#abstract-contracts

**Affected source code:**

- [Enum.sol:5](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Enum.sol#L5)
- [MultiSend.sol:9](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L9)
- [MultiSendCallOnly.sol:8](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L8)
- [Executor.sol:7](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L7)
- [FallbackManager.sol:8](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L8)
- [Singleton.sol:6](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L6)

The following ones should be libraries instead of contracts:

- [SignatureDecoder.sol:5](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/SignatureDecoder.sol#L5)
- [SecuredTokenTransfer.sol:5](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L5)

## **2. Mixing and Outdated compiler**

The pragma version used are:

```
pragma solidity 0.8.12;
```

*Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.17](https://github.com/ethereum/solidity/releases/tag/v0.8.17); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.13](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/):
- Code Generator: Correctly encode literals used in `abi.encodeCall` in place of fixed bytes arguments.

[0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/):

- ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against `calldatasize()` in all cases.
- Override Checker: Allow changing data location for parameters only when overriding external functions.

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

[0.8.17](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/)

- Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

Apart from these, there are several minor bug fixes and improvements.

## **3. Wrong logic in `paymasterContext`**

The method comment states:

> Encodes the paymaster context: sender, token, rate, and fee

However, only `paymasterId` is encoded.

```js
    /**
     * @dev Encodes the paymaster context: sender, token, rate, and fee
     */
    function paymasterContext(
        UserOperation calldata op,
        PaymasterData memory data
    ) internal pure returns (bytes memory context) {
        return abi.encode(data.paymasterId);
    }
```

**Affected source code:**

- [PaymasterHelpers.sol:21-29](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L21-L29)

## **4. Lack of checks**

The following methods have a lack of checks if the received argument is an address, it's good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than `address(0)`.

**Affected source code:**

- [BasePaymaster.sol:20](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L20)
- [BasePaymaster.sol:24](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24)

It doesn't check for range of `_unstakeDelaySec` and can leave locked forever with high time, it also doesn't check that `msg.value` is greater than 0, so a second call with 0, works and could not be expected.

- [StakeManager.sol:59](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L59)

## **5. Centralization risks**

The owner can change the address of `_entryPoint` and affect user deposits when they call the `deposit` method (for example with front-running), thus requiring an unnecessary trust in the project to use.

**Affected source code:**

- [BasePaymaster.sol:24](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24)

## **6. Poxy isues**

There are a lack of checks during the constructor that allows an empty `_implementation`, `fallback` works with `target = address(0)` and the `receive` method does not call `target`, so tokens could be lost if it's called.

```js
    fallback() external payable {
        address target;
        // solhint-disable-next-line no-inline-assembly
        assembly {
            target := sload(_IMPLEMENTATION_SLOT)
            calldatacopy(0, 0, calldatasize())
            let result := delegatecall(gas(), target, 0, calldatasize(), 0, 0)
            returndatacopy(0, 0, returndatasize())
            switch result
            case 0 {revert(0, returndatasize())}
            default {return (0, returndatasize())}
        }
    }
```

If the proxy is deployed with an empty address for `implementation`, the token will be lost.

**Affected source code:**

- [Proxy.sol:15](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L15)

## **7. Integer overflow**

Because the code is inside an `unchecked` block, overflows can occur. If the call to `innerHandleOp` uses a high `opInfo.preOpGas`, `actualGas` might overflow the `_handlePostOp` method when it is in an `unchecked` region.

**Affected source code:**

- [EntryPoint.sol:453](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L453)
- [EntryPoint.sol:469](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L469)

## **8. Loss of tokens**

If the user set the address of `StakeManager` as `withdrawAddress`.

```js
        (bool success,) = withdrawAddress.call{value : withdrawAmount}("");
```

The sender will be `StakeManager` and the tokens are lost forever because it will be deposited as the contract himself.

```js
    receive() external payable {
        depositTo(msg.sender);
    }
```

**Affected source code:**

- [StakeManager.sol:120](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L120)

---

# Non critical

## **9. Open TODO**

The code that contains "open todos" reflects that the development is not finished and that the code can change a posteriori, prior release, with or without audit.

**Affected source code:**

> TODO: copy logic of gasPrice?

- [EntryPoint.sol:255](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255)

## **10. Wrong comment**

The contract `Math` has the following comment:

`Return the log in base 10, following the selected rounding direction, of a positive value.`

But this is not true, because is base 256 as was fixed in https://github.com/OpenZeppelin/openzeppelin-contracts/pull/3916.

**Affected source code:**

- [Math.sol:336](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L336)

## **11. Lack of ACK during owner change**

It's possible to lose the ownership under specific circumstances.

Because of human error it's possible to set a new invalid owner. When you want to change the owner's address it's better to propose a new owner, and then accept this ownership with the new wallet.

**Affected source code:**

- [BasePaymaster.sol:16](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L16)
- [SmartAccount.sol:109](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109)

## **12. Unsafe low level call**

The following low level calls are insecure since they do not verify that the calling address is a contract and not an empty address or an EOA. For that reason, these methods are prone to [phantom methods](https://media.dedaub.com/phantom-functions-and-the-billion-dollar-no-op-c56f062ae49f) attacks.

**Proof of Concept:**

If you try to call the following methods with the following contract `0x000000000000000000000000000000000000000000000` (or any EOA) you can see that the transaction is successful, you have to check that the address is a valid contract and maybe check that the call returns a certain data. Otherwise it is possible to call it and lose tokens or produce undesired errors.

This remind me to this attack https://certik.medium.com/qubit-bridge-collapse-exploited-to-the-tune-of-80-million-a7ab9068e1a0 where we can see:

**Affected source code:**

- [Exec.sol:57](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L57)

## **13. Wong naming**

Authoritative internal methods must use '`_`' before the name. We have an example in [Singleton.sol#L12](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L12) where the methods that require authorization but are internal, a different naming is established.

**Affected source code:**

- [Executor.sol:13](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L13)

## **14. Avoid hardcoded values**

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code.

**Affected source code:**

Use the selector instead of the raw value:

- [DefaultCallbackHandler.sol:22](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L22)
- [DefaultCallbackHandler.sol:32](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L32)
- [DefaultCallbackHandler.sol:41](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L41)
- [SecuredTokenTransfer.sol:17](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L17)
- [Proxy.sol:11](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L11)
