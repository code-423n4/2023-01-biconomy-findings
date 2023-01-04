Potential gas attack using the function multiSend()
=================================

`https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L26-L66`

Because there are no safeguards in place to stop an attacker from sending transactions that use a lot of gas, the multiSend() function has the potential vulnerability that I previously described. The possibility exists that the whole transaction will run out of gas and fail if an attacker is able to send a large number of transactions, each of which uses a sizable quantity of gas.

Mitigation
-----------
One way to address this vulnerability would be to provide or set a gas limit for the multiSend() function or to implement a gas check before runnimg every transaction.