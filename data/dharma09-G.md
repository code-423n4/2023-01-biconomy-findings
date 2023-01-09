**[G-01] Empty blocks should be removed or emit something** 

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`)

*There are 1 instances of this issue:*

```solidity
/SmartAccount.sol
550: receive() external payable {}
```

**[G-02]** **Defined Variables Used Only Once**

Certain variables is defined even though they are used only once.Remove these unnecessary variables to save gas.For cases where it will reduce the readability, one can use comments to help describe what the code is doing.

*There are 2 instances of this issue:*

```solidity
/aa-4337/core/StakeManager.sol
48: function depositTo(address account) public payable {
49:        internalIncrementDeposit(account, msg.value);
50:     DepositInfo storage info = deposits[account];
51:     emit Deposited(account, info.deposit);
52: }

/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
126: PaymasterContext memory data = context.decodePaymasterContext();
127: address extractedPaymasterId = data.paymasterId;
```

**Mitigation**

```diff
48: function depositTo(address account) public payable {
49:        internalIncrementDeposit(account, msg.value);
-50:     DepositInfo storage info = deposits[account];
+50:     emit Deposited(account, deposits[account].deposit);
51: }

-126: PaymasterContext memory data = context.decodePaymasterContext();
+126: address extractedPaymasterId = context.decodePaymasterContext().paymasterId;
```

Don't define variable that is used only once.Details are listed on above PoC.

**[G-03] Splitting `require()` statements that use `&&` saves gas - (saves 8 gas per &&)**

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&The gas difference would only be realized if the revert condition is realized(met).

*There are 3 instances of this issue:*

```solidity
/base/ModuleManager.sol
34: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
49: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
68: require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

```

**[G-04] Function guaranteed to revert when called by normal users can be marked `payable`** 

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

*There are 6 instances of this issue:*

```solidity
/SmartAccount.sol
455: function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
460: function execute(address dest, uint value, bytes calldata func) external onlyOwner{
465: function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{

/paymasters/BasePaymaster.sol
24: function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
75: function addStake(uint32 unstakeDelaySec) external payable onlyOwner {
90: function unlockStake() external onlyOwner {
```

**[G-05] `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables**

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There is 3 instance of this issue:

[Math.sol#L148](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L148)

```solidity
/libs/Math.sol
148: result += 1;

/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
51: paymasterIdBalances[paymasterId] += msg.value;
58: paymasterIdBalances[msg.sender] -= amount;
```

**[G-06] Division by two should use bit shifting** 

`<x> / 2` is the same as `<x> >> 1`. The `DIV` opcode costs 5 gas, whereas `SHR` only costs 3 gas.

*There are 1 instances of this issue:*

```solidity
/libs/Math.sol
36: return (a & b) + (a ^ b) / 2;
```

**[G-07] Use else if instead of if,if,if**

[Math.sol#L261-#L287](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L261-#L287)

*There are 2 instances of this issue:*

```solidity
/libs/Math.sol
261: if (value >= 10**64) {
262:           value /= 10**64;
263:            result += 64;
264:        }
...
285: if (value >= 10**1) {
286:            result += 1;
287:        }

//2 instance

312: if (value >> 128 > 0) {
313:           value >>= 128;
314:           result += 16;
315:        }
...
328: if (value >> 8 > 0) {
329:          result += 1;
330:        }
```

**[G-08] `internal` functions only called once can be inlined to save gas**

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 2 instances of this issue:*

```solidity
/paymasters/BasePaymaster.sol
104: function _requireFromEntryPoint() internal virtual {

/BaseSmartAccount.sol
73: **function _requireFromEntryPoint() internal virtual view {**
```