## L-1: Missing ERC777 in the `supportsInterface(bytes4 interfaceId)` function
- Description: The `supportsInterface(bytes4 interfaceId)` is missing a bool return for ERC777 which is supported.
- Location: [DefaultCallbackHandler.sol#L55](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L55)
- Count: 1

## L-2: Use of unstable specification (EIP-4337)
- Description: Avoid use of unstable specification as it may change in the future. Which may lead to incompatibility with other tools. [source](https://eips.ethereum.org/EIPS/eip-4337)
- Severity: Low
- Remediation: Use finalized specification.
- Location: Project Wide

## L-3: Wrong and misleading name of a specification.(EIP)  
- Description: In the documentation, the EIP-1967 is mentioned as EIP-1167. [source](https://github.com/code-423n4/2023-01-biconomy#proxysol-26-sloc)
- Severity: Low
- Remediation: Correct the name of the specification to EIP-1967.
- Location: [Proxy.sol](https://github.com/code-423n4/2023-01-biconomy#proxysol-26-sloc)

## L-4: Code with no effect. 
- Description: This includes unused function parameters.
- Severity: Low
- Remediation: Check compiler warnings[5667] [2072] and remove unused code.
- Location: [PaymasterHelpers.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol) (Unused function parameter)
- count: 1
