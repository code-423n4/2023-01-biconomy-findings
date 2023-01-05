## Funds can stuck

Contract:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L35

Issue:
Funds can stuck if withdraw address is contract itself

POC:

1. User calls `withdrawTo` function with `withdrawAddress` as StakeManager.sol contract itself

```
function withdrawTo(address payable withdrawAddress, uint256 withdrawAmount) external {
        DepositInfo storage info = deposits[msg.sender];
        require(withdrawAmount <= info.deposit, "Withdraw amount too large");
        info.deposit = uint112(info.deposit - withdrawAmount);
        emit Withdrawn(msg.sender, withdrawAddress, withdrawAmount);
        (bool success,) = withdrawAddress.call{value : withdrawAmount}("");
        require(success, "failed to withdraw");
    }
```

2. This transfer all User deposited ETH to the contract which process it via `receive` function

```
receive() external payable {
        depositTo(msg.sender);
    }
```

3. Since in this case msg.sender is contract itself so deposit is made for StakeManager.sol contract

4. Now there is no way to withdraw these funds

Recommendation:
Kindly revise the receive function to disallow deposit by contract itself

```
receive() external payable {
require(msg.sender!=address(this), "Incorrect sender");
        depositTo(msg.sender);
    }
```

## Stake condition can be revised to allow type(uint112).max

Contract:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L65

Issue:
It seems that currently Staking amount is capped to `type(uint112).max` even though deposit cap is set to `type(uint112).max`. This means the Staking cap could be revised to also allow `type(uint112).max`

Recommendation:
Kindly revise the `addStake` function

```
function addStake(uint32 _unstakeDelaySec) public payable {
...
        require(stake <= type(uint112).max, "stake overflow");
...
}
```

## No way to determine execution status

Contract:
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L232

Issue:
If transaction execution via `execTransaction` does not success but has refundInfo.gasPrice != 0 then there is no way to know if the transaction never suceeded

POC:
1. `execTransaction` function is called for transaction T
2. Transaction T is executed but results in `false` as success

```
success = execute(_tx.to, _tx.value, _tx.data, _tx.operation, refundInfo.gasPrice == 0 ? (gasleft() - 2500) : _tx.targetTxGas);
```

3. Lets say `refundInfo.gasPrice != 0` then below condition returns true and function is executed properly

```
require(success || _tx.targetTxGas != 0 || refundInfo.gasPrice != 0, "BSA013");
            // We transfer the calculated tx costs to the tx.origin to avoid sending it to intermediate contracts that have made calls
            uint256 payment = 0;
            // uint256 extraGas;
            if (refundInfo.gasPrice > 0) {
                //console.log("sent %s", startGas - gasleft());
                // extraGas = gasleft();
                payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);
                emit WalletHandlePayment(txHash, payment);
            }
            // extraGas = extraGas - gasleft();
            //console.log("extra gas %s ", extraGas);
        }
    }
```

4. As we can see that in this case, there is no event mentioning that execution has failed which is incorrect

Recommendation:
If success is false then execute an failure event mentioning the same 