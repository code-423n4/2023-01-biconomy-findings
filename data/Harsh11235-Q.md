# Outdated Compiler

The pragma version used for the contracts 
`pragma solidity 0.8.12`

It is recommended to use the minimum required version of 0.8.17 or contracts may get affected by bugs that have been fixed upto this version, these are as follows :

## [0.8.13](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/)
- abi.encodecall literals handling : `abi.encodeCall` does not handle hex literals and string literals properly, this has been fixed in this version.

## [0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/)
- Size check bug in ABI-Re-encoding fixed : When using `abi.encode` on calldata that contains nested arrays, the length of the nested arrays will be properly validated against `calldatasize()` in every call.
- Data location override check : Overriding functions will not be allowed to change data location of variables from what it is expected to be

## [0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)
- Inline Assembly optimizer :  Keeps the memory side effects of inline assembly
- Code generation bugfix : Does not write dirty values when copying bytes array

## [0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)
- ABI-Re-encoding of tuples : Some parts of dynamic tuple are zeroed when the last component of the tuple is a statically-sized calldata array. This has been fixed in this version.

## [0.8.17](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/)
- Optimizer bugfix: Storage writes are removed before calls to assembly functions terminating external EVM calls before they are completed by the optimizer. This has been fixed in this version



