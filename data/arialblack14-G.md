# GAS OPTIMIZATION REPORT ⛽

## [G-1] `require()`/`revert()` strings longer than 32 bytes cost extras gas.

### Description
Each extra chunk of bytes past the original 32 incurs an MSTORE which costs 3 gas

### ✅ Recommendation
Consider the following require statement:
```Solidity
// condition is boolean
// str is a string
require(condition, str)
```
The string str is split into 32-byte sized chunks and then stored in `memory` using `mstore`, then the `memory` offsets are provided to `revert(offset, length)`. For chunks shorter than 32 bytes, and for low --optimize-runs value (usually even the default value of 200), instead of `push32 val`, where `val` is the 32 byte hexadecimal representation of the string with 0 padding on the least significant bits, the solidity compiler replaces it by `shl(value, short-value))`. Where `short-value` does not have any 0 padding. This saves the total bytes in the deploy code and therefore saves deploy time cost, at the expense of extra 6 gas during runtime. This means that shorter revert strings saves deploy time costs of the contract. Note that this kind of saving is not relevant for high values of --optimize-runs as `push32 value` will not be replaced by a `shl(..., ...)` equivalent by the Solidity compiler.
Going back, each 32 byte chunk of the string requires an extra `mstore`. That is, additional cost for `mstore`, `memory` expansion costs, as well as stack operations. Note that, this runtime cost is only relevant when the revert condition is met.
Overall, shorter revert strings can save deploy time as well as runtime costs.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L77|[require(msg.sender == owner, "Smart Account:: Sender is not authorized");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L77 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L110|[require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L110 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L128|[require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L128 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L18|[require(_baseImpl != address(0), "base wallet address can not be zero");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L18 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L27|[require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L27 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L36|[require(address(_entryPoint) != address(0), "VerifyingPaymaster: Entrypoint can not be zero address");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L36 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L37|[require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L37 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L42|[revert("Deposit must be for a paymasterId. Use depositFor");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L42 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L49|[require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L49 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L50|[require(paymasterId != address(0), "Paymaster Id can not be zero address");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L50 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L66|[require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L66 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L107|[require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L107 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L108|[require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L108 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L109|[require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L109 )|
---


## [G-2] Use shift right/left instead of division/multiplication if possible.

### Description
A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left.
While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas.
urthermore. Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

### ✅ Recommendation
You should change multiplication and division with powers of two to bit shift.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L200|[uint256 startGas = gasleft() + 21000 + msg.data.length * 8;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L200 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L224|[require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L224 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L322|[require(uint256(s) >= uint256(1) * 65, "BSA021");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L322 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L36|[return (a & b) + (a ^ b) / 2;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L36 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L262|[value /= 10**64;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L262 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L266|[value /= 10**32;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L266 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L270|[value /= 10**16;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L270 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L274|[value /= 10**8;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L274 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L278|[value /= 10**4;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L278 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L282|[value /= 10**2;](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L282 )|
---

## [G-3] `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when  it is not possible for them to overflow, for example when used in `for` and `while` loops

### Description
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck [cannot be made inline](https://github.com/ethereum/solidity/issues/10695). 
			Example for loop:

```Solidity
for (uint i = 0; i < length; i++) {
// do something that doesn't change the value of i
}
```

In this example, the for loop post condition, i.e., `i++` involves checked arithmetic, which is not required. This is because the value of i is always strictly less than `length <= 2**256 - 1`. Therefore, the theoretical maximum value of i to enter the for-loop body `is 2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. You should manually do this by:

```Solidity
for (uint i = 0; i < length; i = unchecked_inc(i)) {
	// do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
	unchecked {
		return i + 1;
	}
}
```
Or just:

```Solidity
for (uint i = 0; i < length;) {
	// do something that doesn't change the value of i
	unchecked { i++; 	}
}
```
Note that it’s important that the call to `unchecked_inc` is inlined. This is only possible for solidity versions starting from `0.8.2`.

Gas savings: roughly speaking this can save 30-40 gas per loop iteration. For lengthy loops, this can be significant!
(This is only relevant if you are using the default solidity checked arithmetic.)


### ✅ Recommendation
Use the `unchecked` keyword

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L74|[for (uint256 i = 0; i < opslen; i++) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L74 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L80|[for (uint256 i = 0; i < opslen; i++) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L80 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100|[for (uint256 i = 0; i < opasLen; i++) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L112|[for (uint256 i = 0; i < opslen; i++) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L112 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L134|[for (uint256 i = 0; i < opslen; i++) {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L134 )|
---

## [G-4] Using storage instead of memory for structs/arrays saves gas.

### Description
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.
Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

### ✅ Recommendation
Use storage for `struct/array`

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L71|[UserOpInfo[] memory opInfos = new UserOpInfo[](opslen);](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L71 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L104|[UserOpInfo[] memory opInfos = new UserOpInfo[](totalOps);](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L104 )|
---

## [G-5] Empty blocks should be removed or emit something.

### Description
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified
```solidity
if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}
```

### ✅ Recommendation
Empty blocks should be removed or emit something (The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550|[receive() external payable {}](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L119|[try aggregator.validateSignatures(ops, opa.signature) {}](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L119 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L458|[try IPaymaster(paymaster).postOp{gas : mUserOp.verificationGasLimit}(mode, context, actualGasCost) {}](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L458 )|
---

## [G-6] Functions guaranteed to revert when called by normal users  can be marked payable.

### Description
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost. Please check out this [article](https://coinsbench.com/solidity-payable-vs-regular-functions-a-gas-usage-comparison-b4a387fe860d)

### ✅ Recommendation
TODO: You should add the keyword `payable` to those functions.

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449|[function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455|[function pullTokens(address token, address dest, uint256 amount) external onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460|[function execute(address dest, uint value, bytes calldata func) external onlyOwner{](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L75|[function addStake(uint32 unstakeDelaySec) external payable onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L75 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90|[function unlockStake() external onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99|[function withdrawStake(address payable withdrawAddress) external onlyOwner {](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99 )|
---

## [G-7] Use two `require` statements instead of operator `&&` to save gas.

### Description
Usage of double require will save you around 10 gas with the optimizer enabled.
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper.
 Example:
```Solidity
contract Requires {
uint256 public gas;
			
				function check1(uint x) public {
					gas = gasleft();
					require(x == 0 && x < 1 ); // gas cost 22156
					gas -= gasleft();
				}
			
				function check2(uint x) public {
					gas = gasleft();
					require(x == 0); // gas cost 22148
					require(x < 1);
					gas -= gasleft();
	}
}
```


### ✅ Recommendation
Consider changing one `require()` statement to two `require()` to save gas

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34|[require(module != address(0) && module != SENTINEL_MODULES, "BSA101");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49|[require(module != address(0) && module != SENTINEL_MODULES, "BSA101");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68|[require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68 )|
---

## [G-8] `abi.encode()` is less efficient than `abi.encodePacked()`

### Description
`abi.encode` will apply ABI encoding rules. Therefore all elementary types are padded to 32 bytes and dynamic arrays include their length. Therefore it is possible to also decode this data again (with `abi.decode`) when the type are known.

`abi.encodePacked` will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. For more info see the Solidity docs for packed mode.

For the input of the `keccak` method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.
For example:
`abi.encodePacked(address(0x0000000000000000000000000000000000000001), uint(0))` encodes to the same as `abi.encodePacked(uint(0x0000000000000000000000000000000000000001000000000000000000000000), address(0))` 
and `abi.encodePacked(uint[](1,2), uint[](3))` encodes to the same as `abi.encodePacked(uint[](1), uint[](2,3))`
Therefore these examples will also generate the same hashes even so they are different inputs.
On the other hand you require less memory and therefore in most cases abi.encodePacked uses less gas than abi.encode.

### ✅ Recommendation
Use `abi.encodePacked()` where possible to save gas

### 🔍 Findings:
| | |
|---|---|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L136|[return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L136 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L431|[abi.encode(](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L431 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L197|[return keccak256(abi.encode(userOp.hash(), address(this), block.chainid));](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L197 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L28|[return abi.encode(data.paymasterId);](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L28 )|
|2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L80|[return keccak256(abi.encode(](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L80 )|
---
