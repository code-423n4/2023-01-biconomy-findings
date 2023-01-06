Potential gas attack using the function multiSend()
=================================

`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L26-L66`

Because there are no safeguards in place to stop an attacker from sending transactions that use a lot of gas, the multiSend() function has the potential vulnerability that I previously described. The possibility exists that the whole transaction will run out of gas and fail if an attacker is able to send a large number of transactions, each of which uses a sizable quantity of gas.

Mitigation
-----------
One way to address this vulnerability would be to provide or set a gas limit for the multiSend() function or to implement a gas check before running every transaction.


Potential vulnerability in the function fallback()
===============================

`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L32-L53`

The fallback function does not check the transaction value and will transmit all fallback calls with data, regardless of the value supplied, which is a potential vulnerability in the FallbackManager contract. If an attacker is able to send a fallback call with a big value and abuse the contract, this could lead to a vulnerability.


Gas exhaustion attack in the contract  SmartAccountNoAuth.sol
=========================================
`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L200`

The potential vulnerability lies where an attacker can potentially manipulate the `msg.data` field and consume a huge amount of the contract gas. Since `msg.data` is a byte array and the `length` property returns the number of bytes in the array. If an attacker calls a function that uses large amounts of data in the `msg.data` the array might consume large amounts of gas as the data is being processed and if it fails state changes willbe reverted.

Mitigation
-----------
before proceeding with the execution perform cheks to ensure that the `msg.data.length` is within a reasonable range.





