# Biconomy Low + QA + Gas Optimizations Report - Optimiz00r

### [StakeManager.sol] (https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol)

#### LOW - [L48] depositTo function: 
 - No validation that msg.value should be != 0. Users depositing with msg.value = 0 will only make them lose on gas.
add: `require(msg.value!=0,"Invalid Deposit Value");`
#### GAS - [L50] despositTo function: 
- "DepositInfo storage info = deposits[account]" Not necessary, since only the value needed for event emit on L51 can be accessed directly with "deposits[account].deposit". Saves a bit of gas.

My suggestion:
`function depositTo(address account) external payable {`
	`require(msg.value!=0,"Invalid Deposit Value");`
        `internalIncrementDeposit(account, msg.value);`
        `emit Deposited(account, deposits[account].deposit);`
`}`

#### GAS - [L48] depositTo function:
- external instead of public modifier, saves gas 
#### GAS - [L59] addStake function:
- external instead of public modifier, saves gas


-------------
### [SmarAccount.sol] (https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

#### LOW - [L127] updateEntryPoint function:
 - Should check if address provided is a contract (expected EntryPoint contract), to prevent from accidentally setting it to an EOA.
#### GAS - [L481] _call function:
  - if called from executeBatch, one revert will fail all previous successfully executed calls, therefore wasting gas. Consider using `try catch` when called from executeBatch function.
#### GAS - [L197] execTransaction function:
 - external instead of public modifier, saves gas
#### GAS - [L525] addDeposit function:
 - external instead of public modifier, saves gas
-----------
### [EntryPoint.sol] (https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)

#### GAS/SYNTAX - [L93] handleAggregateOps function:
 - combining two `for` loops into one is possible, leading to gas savings:
    [L107] `for (uint256 a = 0; a < opasLen; a++) `
    [L128] `for (uint256 a = 0; a < opasLen; a++) `
#### GAS - [L93] handleAggregateOps function:
- external instead of public modifier, saves gas

#### GAS/SYNTHAX - [L68] handleOps function:
- combining two `for` loops into one is possible, leading to gas savings:
    [L74] `for (uint256 i = 0; i < opslen; i++)` 
    [L80] `for (uint256 i = 0; i < opslen; i++)` 
#### GAS - [L68] handleOps function:
- external instead of public modifier, saves gas
