## 1 . Redundant check in init() :-

Summary :-
In init() function  in line 171 it checks whether is _handle is not equal to addresss(0) .But in again in line 174 if statements it checks _handle is not equal to address(0) then call internalSetFallbackHandler() .So it is not necessary to check again in line 174 it can directly call  internalSetFallbackHandler() .

code snippet:-
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L174