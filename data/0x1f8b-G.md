- [Gas](#gas)
    - [**1. Remove unnecessary variables**](#1-remove-unnecessary-variables)
    - [**2. Optimize internalIncrementDeposit**](#2-optimize-internalincrementdeposit)
    - [**3. Optimize unlockStake**](#3-optimize-unlockstake)
    - [**4. Reorder structure layout**](#4-reorder-structure-layout)
    - [**5. Use the unchecked keyword**](#5-use-the-unchecked-keyword)

# Gas

## **1. Remove unnecessary variables**

The following state variables can be removed without affecting the logic of the contract since they are not used and/or are redundant because they could be used inline.

**Affected source code:**

- In the following case is used, but also is added: [VerifyingSingletonPaymaster.sol:99](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L99)
- [BasePaymaster.sol:50](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L50)
- [VerifyingSingletonPaymaster.sol:124](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L124)

## **2. Optimize `internalIncrementDeposit`**

It's possible to optimize the `internalIncrementDeposit` method like follows:

```diff
-   function internalIncrementDeposit(address account, uint256 amount) internal {
+   function internalIncrementDeposit(address account, uint256 amount) internal returns (uint112 ret) {
        DepositInfo storage info = deposits[account];
        uint256 newAmount = info.deposit + amount;
        require(newAmount <= type(uint112).max, "deposit overflow");
+       ret = uint112(newAmount);
+       info.deposit = ret;
-       info.deposit = uint112(newAmount);
    }
    function depositTo(address account) public payable {
-       internalIncrementDeposit(account, msg.value);
+       emit Deposited(account, internalIncrementDeposit(account, msg.value));
-       DepositInfo storage info = deposits[account];
-       emit Deposited(account, info.deposit);
    }
```

**Gas diff:**

In red the old version, in green with the applied changes:

```diff
- rcpt.gasUsed= 155168 0x31494a79c7dcc9163be9fc3c9f79c43547e4b39050c8b14920f02a5bcb97588d
+ rcpt.gasUsed= 154996 0x40cc316df0a2fb9d6261915aea960f905dd63e9da797f3d5e24b0fe68149c1c3
-   == actual gasUsed (from tx receipt)= 155168
+	== actual gasUsed (from tx receipt)= 154996
...
-   == actual gasUsed (from tx receipt)= 383762
+   == actual gasUsed (from tx receipt)= 383578
-   == calculated gasUsed (paid to beneficiary)= BigNumber { value: "334787" }
+	== calculated gasUsed (paid to beneficiary)= BigNumber { value: "334599" }
```

**Affected source code:**

- [StakeManager.sol:38-43](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L38-L43)

## **3. Optimize `unlockStake`**

You can use `withdrawTime != 0` as a `staked` flag, so you could remove that field from the `DepositInfo` structure, saving space and gas.

**Affected source code:**

- [StakeManager.sol:86](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L86)

## **4. Reorder structure layout**

The following structures could be optimized moving the position of certain values in order to save some storage slots:

```diff
struct Transaction {
        address to;
+       Enum.Operation operation;
        uint256 value;
        bytes data;
-       Enum.Operation operation;
        uint256 targetTxGas;
    }
```

**Reference:**

- https://ethdebug.github.io/solidity-data-representation

> Enums are represented by integers; the possibility listed first by 0, the next by 1, and so forth. An enum type just acts like uintN, where N is the smallest legal value large enough to accomodate all the possibilities.

- https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html#storage-inplace-encoding

**Gas diff:**

In red the old version, in green with the applied changes:

```diff
mint tokens to owner address..
{
  to: '0x0ed64d01D0B4B655E410EF1441dD677B695639E7',
  value: 0,
  data: '0xa9059cbb0000000000000000000000003c44cdddb6a900fa2b585dd299e03d12fa4293bc0000000000000000000000000000000000000000000000008ac7230489e80000',
  operation: 0,
  targetTxGas: 0,
  baseGas: 0,
  gasPrice: 0,
  tokenGasPriceFactor: 1,
  gasToken: '0x0000000000000000000000000000000000000000',
  refundReceiver: '0x0000000000000000000000000000000000000000',
  nonce: BigNumber { value: "0" }
}
- estimated gas to be used  102719
+ estimated gas to be used  102710
- Real txn gas used:  98875
+ Real txn gas used:  98866

targetTxGas estimation part 1:  36544
- estimated gas to be used  135491
+ estimated gas to be used  135482
{
-  baseGas: 103947,
+  baseGas: 103938,
```

**Affected source code:**

- [BaseSmartAccount.sol:16](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L16)

## **5. Use the `unchecked` keyword**

When an underflow or overflow cannot occur, one might conserve gas by using the `unchecked` keyword to prevent unnecessary arithmetic underflow/overflow tests.

```diff
    function simulateValidation(UserOperation calldata userOp) external {
        uint256 preGas = gasleft();

        UserOpInfo memory outOpInfo;

        (address aggregator, uint256 deadline) = _validatePrepayment(0, userOp, outOpInfo, SIMULATE_FIND_AGGREGATOR);
        uint256 prefund = outOpInfo.prefund;
-       uint256 preOpGas = preGas - gasleft() + userOp.preVerificationGas;
+       uint256 preOpGas;
+       unchecked {
+           preOpGas = preGas - gasleft() + userOp.preVerificationGas;
+       }
        StakeInfo memory paymasterInfo = getStakeInfo(outOpInfo.mUserOp.paymaster);
        StakeInfo memory senderInfo = getStakeInfo(outOpInfo.mUserOp.sender);
        bytes calldata initCode = userOp.initCode;
        address factory = initCode.length >= 20 ? address(bytes20(initCode[0 : 20])) : address(0);
        StakeInfo memory factoryInfo = getStakeInfo(factory);

        if (aggregator != address(0)) {
            AggregatorStakeInfo memory aggregatorInfo = AggregatorStakeInfo(aggregator, getStakeInfo(aggregator));
            revert SimulationResultWithAggregation(preOpGas, prefund, deadline, senderInfo, factoryInfo, paymasterInfo, aggregatorInfo);
        }
        revert SimulationResult(preOpGas, prefund, deadline, senderInfo, factoryInfo, paymasterInfo);

    }
```

**Gas diff:**

In red the old version, in green with the applied changes:

```diff
        √ legacy mode (maxPriorityFee==maxFeePerGas) should not use "basefee" opcode
-    == est gas= 114321
+    == est gas= 114297
-   rcpt.gasUsed= 111704 0x914c39060be62a5980f0ffa05b56214fc3301dbc44b7ba872afd0753058b95ca
+   rcpt.gasUsed= 111680 0xa023ccb107b9dc80adcb3d031d48664146215e76e030839f0d8033f100d44faf
-   	== actual gasUsed (from tx receipt)= 111704
+       == actual gasUsed (from tx receipt)= 111680

      create account
        √ should reject if account not funded
-   == create gasUsed= 381974
+   == create gasUsed= 381962
-   == actual gasUsed (from tx receipt)= 381974
+	== actual gasUsed (from tx receipt)= 381962

- |  [90mEntryPoint[39m               ·  handleAggregatedOps         ·     [36m163341[39m  ·     [31m345337[39m  ·     245824  ·            [90m6[39m  ·          [32m[90m-[32m[39m  │
+ |  [90mEntryPoint[39m               ·  handleAggregatedOps         ·     [36m163341[39m  ·     [31m345337[39m  ·     245822  ·            [90m6[39m  ·          [32m[90m-[32m[39m  │
- |  [90mEntryPoint[39m               ·  handleOps                   ·     [36m101633[39m  ·     [31m507641[39m  ·     256369  ·           [90m28[39m  ·          [32m[90m-[32m[39m  │
+ |  [90mEntryPoint[39m               ·  handleOps                   ·     [36m101597[39m  ·     [31m507653[39m  ·     256364  ·           [90m28[39m  ·          [32m[90m-[32m[39m  │
```

**Affected source code:**

- [EntryPoint.sol:233](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L233)
- [SmartAccount.sol:291](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L291)
- [SmartAccount.sol:372](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L372)
