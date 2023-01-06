# Gas Optimizations

## Summary 

In total, `5` gas optimizations are found. Please also noted that the following do not inclue those included in the C4Audit output [here](https://gist.github.com/Picodes/0a53b4abfc71e0b9998e8b09aa283fb3).

## [G-01] USE REQUIRE INSTEAD OF ASSERT FOR ERROR HANDLING

The `assert()` and `require()` functions are a part of error handling in Solidity. They both check conditions and throw an error if they are not met.

The main difference between the two is the way they handle gas and changes made to the contract. 

When `assert()` is used and the condition is false, all remaining gas is consumed and all changes made to the contract and any sub-calls are reversed. On the other hand, if `require()` is used and the condition is false, only changes made to the contract are reversed and any remaining gas fees are refunded.

_instance (2)_:

```diff
file: contracts/smart-contract-wallet/common/Singleton.sol, line 13

+   require(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
-   assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13)

```diff
file: contracts/smart-contract-wallet/Proxy.sol, line 16

+   require(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
-   assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L16)

## [G-02] USE CALLDATA INSTEAD OF MEMORY IN `innerHandleOp()`

Using the `memory` keyword instead of `calldata` in the `innerHandleOp()` function in `EntryPoint.sol` is possible and can potentially reduce gas costs. The recommended changes are outlined below.

_instance (1)_:

```diff
file: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol, line 168 - 190

+   function innerHandleOp(bytes calldata callData, UserOpInfo calldata opInfo, bytes calldata context) external returns (uint256 actualGasCost) {
-   function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost) {
        uint256 preGas = gasleft();
        require(msg.sender == address(this), "AA92 internal call only");
+       MemoryUserOp calldata mUserOp = opInfo.mUserOp;
-       MemoryUserOp memory mUserOp = opInfo.mUserOp;

        IPaymaster.PostOpMode mode = IPaymaster.PostOpMode.opSucceeded;
        if (callData.length > 0) {

            (bool success,bytes memory result) = address(mUserOp.sender).call{gas : mUserOp.callGasLimit}(callData);
            if (!success) {
                if (result.length > 0) {
                    emit UserOperationRevertReason(opInfo.userOpHash, mUserOp.sender, mUserOp.nonce, result);
                }
                mode = IPaymaster.PostOpMode.opReverted;
            }
        }

    unchecked {
        uint256 actualGas = preGas - gasleft() + opInfo.preOpGas;
        //note: opIndex is ignored (relevant only if mode==postOpReverted, which is only possible outside of innerHandleOp)
        return _handlePostOp(0, mode, opInfo, context, actualGas);
    }
    }
```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L168)

## [G-03] REORDER STRUCTURE LAYOUR TO SAVE SLOT STORAGES

The layout of the following structs could be optimized by rearranging the positions of certain values in order to reduce the number of storage slots required.

This would lead to a more efficient use of storage resources and potentially lower gas costs for interactions involving these structs.

_instance (3)_:

```diff
file: contracts/smart-contract-wallet/BaseSmartAccount.sol, line 12 - 18

    struct Transaction {
        address to;
+       bytes data;
+       Enum.Operation operation;
        uint256 value;
-       bytes data;
-       Enum.Operation operation;
        uint256 targetTxGas;
    }
```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L12)

```diff
file: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol, line 145 - 154

    struct MemoryUserOp {
        address sender;
+       address paymaster;
        uint256 nonce;
        uint256 callGasLimit;
        uint256 verificationGasLimit;
        uint256 preVerificationGas;
-       address paymaster;
        uint256 maxFeePerGas;
        uint256 maxPriorityFeePerGas;
    }
```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L145)

```diff
file: contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol, line 53 - 59

    struct DepositInfo {
        uint112 deposit;
-       bool staked;
        uint112 stake;
        uint32 unstakeDelaySec;
+       bool staked;
        uint64 withdrawTime;
    }
```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L53)

## [G-04] SHIFT RIGHT OR LEFT INSTEAD OF DIVIDING OR MULTIPLY BY 2

Shifting a value one position to the right is equivalent to performing a division by two.

The `SHR` opcode is more efficient than the `DIV` opcode, as it only requires 3 gas instead of 5. It is also useful in Solidity as it can be used to avoid the prohibition on division by zero.

The left shift can be used to perform multiplication by 2 as an optimization.

_instance (1)_:

```diff
file: contracts/smart-contract-wallet/libs/Math.sol, line 36

+   return (a & b) + (a ^ b) >> 1;
-   return (a & b) + (a ^ b) / 2;
```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L36)

* Noted that `average()` is not used in the core contracts logic but could be worth mentioning because it is within the scope.

## [G-05] USE `unchecked{ i++; }` FOR COUNTER IN LOOPS

Using `unchecked` for the counters is possible because they are bounded.

_instance (5)_:

```diff
file: contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol, line 93 - 142

    function handleAggregatedOps(
        UserOpsPerAggregator[] calldata opsPerAggregator,
        address payable beneficiary
    ) public {

        uint256 opasLen = opsPerAggregator.length;
        uint256 totalOps = 0;
+       for (uint256 i = 0; i < opasLen;) {
-       for (uint256 i = 0; i < opasLen; i++) {
            totalOps += opsPerAggregator[i].userOps.length;
+           unchecked { i++; }
        }

        UserOpInfo[] memory opInfos = new UserOpInfo[](totalOps);

        uint256 opIndex = 0;
+       for (uint256 a = 0; a < opasLen) {
-       for (uint256 a = 0; a < opasLen; a++) {
            UserOpsPerAggregator calldata opa = opsPerAggregator[a];
            UserOperation[] calldata ops = opa.userOps;
            IAggregator aggregator = opa.aggregator;
            uint256 opslen = ops.length;
+           for (uint256 i = 0; i < opslen;) {
-           for (uint256 i = 0; i < opslen; i++) {
                _validatePrepayment(opIndex, ops[i], opInfos[opIndex], address(aggregator));
                opIndex++;
            }

            if (address(aggregator) != address(0)) {
                // solhint-disable-next-line no-empty-blocks
                try aggregator.validateSignatures(ops, opa.signature) {}
                catch {
                    revert SignatureValidationFailed(address(aggregator));
                }
+               unchecked { i++; }
            }
+           unchecked { a++; }
        }

        uint256 collected = 0;
        opIndex = 0;
+       for (uint256 a = 0; a < opasLen;) {
-       for (uint256 a = 0; a < opasLen; a++) {
            UserOpsPerAggregator calldata opa = opsPerAggregator[a];
            emit SignatureAggregatorChanged(address(opa.aggregator));
            UserOperation[] calldata ops = opa.userOps;
            uint256 opslen = ops.length;

+           for (uint256 i = 0; i < opslen;) {
-           for (uint256 i = 0; i < opslen; i++) {
                collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);
                opIndex++;
+               unchecked { i++; }
            }
        }
        emit SignatureAggregatorChanged(address(0));

        _compensate(beneficiary, collected);
+       unchecked { a++; }
    }
```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L93)

```diff
file: contracts/smart-contract-wallet/base/ModuleManager.sol, line 121 - 125

    while (currentModule != address(0x0) && currentModule != SENTINEL_MODULES && moduleCount < pageSize) {
        array[moduleCount] = currentModule;
        currentModule = modules[currentModule];
+       unchecked { moduleCount++; }
-       moduleCount++;
    }

```

[Link to code](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L121)