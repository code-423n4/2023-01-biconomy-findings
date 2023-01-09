# Solidity version out of date
``` 
pragma solidity 0.8.12;
```
## Affected files
- All files in scope

## Synopsis
Using an out-of-date Solidity compiler can introduce vulnerabilities and bugs into your smart contracts for a few reasons:

- Outdated compilers may not support the latest features or optimizations, which can result in contracts that are less efficient or less secure.
- Outdated compilers may not fix known bugs or vulnerabilities in the compiler itself, which could allow an attacker to exploit these issues to compromise your contract. 
- Newer versions of Solidity may introduce breaking changes that can cause existing contracts compiled with an older version to behave in unexpected ways. This can lead to vulnerabilities if the contract relies on certain assumptions that are no longer valid in the newer version of Solidity.

## Details
Some bugs present in version 0.8.12:
- SOL-2022-6
- SOL-2022-5
- SOL-2022-2
- SOL-2022-1

*you can find a more complete list of all the known vulnerabilities [here](https://docs.soliditylang.org/en/v0.8.17/bugs.html)*
## Recommendation
It is generally a good idea to use the latest version of Solidity when writing or deploying contracts, as this will ensure that you have access to the latest features and optimizations and that you are not using a version of the compiler that is known to have vulnerabilities.

