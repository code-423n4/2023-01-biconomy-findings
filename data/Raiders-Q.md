## LOW/NON-CRITICAL FINDING's

### Title 1: Assert Requires State Change

#### Impact
Statements inside require and assert should not change state through any function call or keyword.
The contract was found to be making state changes inside the require or assert statements.

#### PoC - Vulnerable Line of code
- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L371

#### Remediation
It is recommended to not make any state changes inside assert or require statements and to always follow the pattern of check-effects-interactions.
assert should only be used to check invariants and should be replaced with require for user input and return values.

### TITLE 2: USE OF FLOATING PRAGMA

#### Vulnerable Endpoint 1: 
- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L6

#### Vulnerable Endpoint 2: 
- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L2

#### Impact
Solidity source files indicate the versions of the compiler they can be compiled with using a pragma directive at the top of the solidity file. This can either be a floating pragma or a specific compiler version.
The contract was found to be using a floating pragma which is not considered safe as it can be compiled with all the versions described.
The following affected files were found to be using floating pragma:- `^0.8.12`

#### Remediation
It is recommended to use a fixed pragma version, as future compiler versions may handle certain language constructions in a way the developer did not foresee.
Using a floating pragma may introduce several vulnerabilities if compiled with an older version.
The developers should always use the exact Solidity compiler version when designing their contracts as it may break the changes in the future.
Instead of `^0.8.12` use pragma solidity `0.8.7`, which is a stable and recommended version right now.

### TITLE 3: MISSING EVENTS

#### Impact
Events are inheritable members of contracts. When you call them, they cause the arguments to be stored in the transaction’s log—a special data structure in the blockchain.
These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.
The contract `BaseSmartAccount` was found to be missing these events on the function `validateUserOp` which would make it difficult or impossible to track these transactions off-chain.

#### PoC - Vulnerable Line of code
- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L60-L68

#### Remediation
Consider emitting events for the functions mentioned above. It is also recommended to have the addresses indexed.

### TITLE 4: REWRITE ON ASSEMBLY CALL

#### Impact:
The Smart Contract is using inline assembly - specifically the instructions of the CALL family such as `delegatecall` and `staticcall`. It should be manually checked for data handling and that it does not overwrite the input data by writing output data over it. In case the arbitrary address is passed inside the call, the return value may differ from the expected one.

#### POC
- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L28

#### Remediation
Depending on how the `CALL` is handled in the code, if the output is being overwritten on the same memory space as the input, consider saving the output to a separate area of memory.

### TITLE 5: REQUIRE WITH EMPTY MESSAGE

#### Impact:
A `require` statement was detected with an empty message. It takes two parameters and the message part is optional. This is shown to the user when and if the `require` statement evaluates to `false`. This message gives more information about the statement and why it gave a `false` response.

#### POC
- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L371

#### Remediation
It is recommended to add a descriptive message, no longer than 32 bytes, inside the require statement to give more detail to the user about why the condition failed.

### TITLE 6: BLOCK VALUES AS A PROXY FOR TIME

#### Impact:
Contracts often need access to time values to perform certain types of functionality. Values such as `block.timestamp` and `block.number` can be used to determine the current time or the time delta. However, they are not recommended for most use cases.


For `block. Number`, as Ethereum block times are generally around 14 seconds, the delta between blocks can be predicted. The block times, on the other hand, do not remain constant and are subject to change for a number of reasons, e.g., fork reorganizations and the difficulty bomb.


Due to variable block times, `block.number` should not be relied on for precise calculations of time.

#### PoC
- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L101

#### Remediation
Smart contracts should be written with the idea that block values are not precise, and their use can have unexpected results. Alternatively, oracles can be used.

### TITLE 7: MISSING INDEXED KEYWORDS IN EVENTS

#### IMPACT:
Events are essential for tracking off-chain data and when the event paraemters are `indexed` they can be used as filter options which will help getting only the specific data instead of all the logs.

#### PoC
- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L67

#### Remediation

Consider adding `indexed` keyword to crucial event parameters that could be used in off-chain tracking. Do remember that the `indexed` keyword costs more gas.

### TITLE 8: SIGNATURE MALLEABILITY

#### IMPACT:
The function `ecrecover` allows you to convert a valid signature into a different valid signature without requiring knowledge of the private key. It is usually not a problem unless you use signatures to identify items or require them to be uniquely recognizable.


Therefore, depending on the function of the code, this may lead to discrepancies and faulty logic.

#### POC:
- https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L347

It is recommended to use OpenZeppelin’s ECDSA library that has a wrapper around ecrecover that mitigates this issue. The data signer can be recovered using ECDSA.recover, and its address can be compared to verify the signature.
 - https://docs.openzeppelin.com/contracts/2.x/api/cryptography#ECDSA

