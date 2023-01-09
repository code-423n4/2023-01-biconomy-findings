## FUNCTIONS WITH SAME NAME AND COMMENTS CAN BE CONFUSING

### Description:

The functions here https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L41 and https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L47 have the same name (nonce) and the same comments too , they should be either named differently (maybe name the second one as nonce2D ) , or provide better comments.

## setOwner FUNCTION SHOULD BE A TWO STEP PROCESS

### Description:

Functions where we set a high privilege role and that function is only callable by that high privilege role , should be executed in two steps i.e first step where we set the pending owner(the new owner) , and another function from where the pending owner can accept the role. This way the owner can mistakenly set the wrong pending owner but then has the ability to change that again to the correct one.

Affected instances:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109

##  ONE CHECK HAS BEEN MADE TWICE INSIDE THE SAME FUNCTION

### Description:

The function here https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166 checks if the handler is a 0 address here https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171 but then again checks that same condition here https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L174

## USE OF tx.origin SHOULD BE AVOIDED

### Description:

Use of tx.origin is considered to be dangerous as it can be bypassed using fishing. Read more https://hackernoon.com/hacking-solidity-contracts-using-txorigin-for-authorization-are-vulnerable-to-phishing
Store the desired address in a variable using msg.sender instead  here https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L257

## PRIVATE/INTERNAL FUNCTIONS SHOULD START WITH AN UNDERSCORE

### Description:

Every internal/private function should start with an underscore like _internalFunction.

Affected instances:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L247


## TYPO IN EntryPoint contract

### Description:

There's a typo here https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L43 , `into` has been used twice.

## INSTEAD OF USING unchecked FOR WHOLE FUNCTION , USE IT WHERE IT IS REQUIRED

### Description:

In the codebase , at alot of places unchecked has been used for whole function blocks , instead use it at specific code lines where required.

Affected code:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L442
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L485
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L73
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L249
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L291
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L350
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L417


 
## Missing docstrings

### Description:

Many functions in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention,
which is fundamental to correctly assess not only security, but also correctness. Additionally, docstrings improve
readability and ease maintenance. They should explicitly explain the purpose or intention of the functions,
the scenarios under which they can fail, the roles allowed to call them, the values returned and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API.
Functions implementing sensitive functionality, even if not public, should be clearly documented as well.
When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

Affected instances(not all):

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L16
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L20
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L10 (no comment about the return value)






