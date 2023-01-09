
# 1. Save gas with `payable` functions

Marking functions as `payable` will be cheaper (by ~20 gas) than using non-`payable` functions because the Solidity compiler inserts a check into non-`payable` functions that requires `msg.value` to be zero.

For instance, the code at [SmartAccount.sol#L449](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449) can be refactored as:

```solidity
Line 449:    function transfer(address payable dest, uint amount) external payable nonReentrant onlyOwner {
```

> **Note**: Although this optimization can save gas, it is important to be aware of the security considerations involving Ether held in contracts that it introduces.
> More information on this topic can be found in the [Solidity Compiler Discussion](https://github.com/ethereum/solidity/issues/12539).

Affected line of code:

- [SmartAccount.sol#L449](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449)

# 2. Splitting `require()` Statements That Use `&&` Saves Gas - (Saves ~`3` Gas per `&&`)

Instead of using the `&&` operator in a single require statement to check multiple conditions, using various require statements with 1 condition per require statement will save ~3 GAS per `&&`.

Affected line of code:

- [ModuleManager.sol#L34](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34)
- [ModuleManager.sol#L49](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49)
- [ModuleManager.sol#L68](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68)

# 3. An internal function that can reduce to 1 line can be omitted

An internal function that can be reduced to 1 line of code can be omitted (if possible) will save gas, this is because you don't need to call a function to do whatever is inside of the code block.

For example on lines [461](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L461), and [466](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L466) the function `_requireFromEntryPointOrOwner()` on lines [494-496](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L494-L496) is called

```solidity
contracts/smart-contract-wallet/SmartAccount.sol
Line 461:    _requireFromEntryPointOrOwner();
Line 466:    _requireFromEntryPointOrOwner();

Line 494:    function _requireFromEntryPointOrOwner() internal view {
Line 495:        require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
Line 496:    }
```

However you can simply omit the function `_requireFromEntryPointOrOwner()` and move what was in `_requireFromEntryPointOrOwner()` code block and move it to lines [461](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L461), and [466](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L466) like so

```solidity
Line 461:    require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
Line 466:    require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
```

# 4. Regular addition/subtraction assignment saves gas

Using standard addition or subtraction assignment (`x = x + y` or `x = x - y`) rather than the shorthand versions (`x += y` or `x -= y`) saves gas. This can save approximately 22 gas per occurrence.

Affected line of code:

- [EntryPoint.sol#L81](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L81)

# 5. `>`/`<` is cheaper than `>=`/`<=`

Using the `>`/`<` operator requires fewer opcodes and therefore costs less gas than using the `>=`/`<=` operator. This is demonstrated in the tweet linked below:

https://twitter.com/GalloDaSballo/status/1543729467465629696

This optimization can be applied to the following lines of code:

- [SmartAccount.sol#L322](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L322)

# 6. Uncheck arithmetics operations that can’t underflow/overflow

Solidity version 0.8+ has an implicit overflow and underflow check on unsigned integers.

When an overflow or an underflow isn’t possible, some gas can be saved using an unchecked block.

For example, the code at [SmartAccount.sol#L216](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L216)
can be refactored to as:

```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol
unchecked {
    nonces[batchId]++;
}
```

There is 1 instance of this issue:

- [SmartAccount.sol#L216](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L216)

# 7.  Using `Storage` Instead of `Memory` for Structs/Arrays Saves Gas

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array.
If the fields are read from the new `memory` variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

As an example, The code at [EntryPoint.sol#L171](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L171) can be refactored as follows:

```solidity
Line 171:    MemoryUserOp storage mUserOp = opInfo.mUserOp;
```

Affected line of code:

- [EntryPoint.sol#L171](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L171)
- [EntryPoint.sol#L229](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L229)
- [EntryPoint.sol#L234-L235](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L234-L235)
- [EntryPoint.sol#L238](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L238)
- [EntryPoint.sol#L241](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L241)
- [EntryPoint.sol#L293](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L293)
- [EntryPoint.sol#L351](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L351)
- [EntryPoint.sol#L359](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L359)
- [EntryPoint.sol#L444](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L444)
