QA1. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L397
There is a typo here, the comparison should be against ``type(uint128).max`` instead of ``type(uint120).max`` since the documentation says "validate all numeric values in userOp are well below 128 bit".

```
require(maxGasValues <= type(uint128).max, "AA94 gas values overflow");

```

QA2: https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L75
The modifier ``onlyOwner`` is not necessary, just like the ``deposit()`` function. 

QA3: https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99
Zero address check is necessary for ``withdrawAddress`` to avoid losing funding to the zero address.

QA4: https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L2
Lock all contracts to the most recent version of Solidity, 0.8.17.

QA5. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L55-L61
The function should also indicate that it supports the interface of ``ERC777TokensRecipient`` as well.

```
function supportsInterface(bytes4 interfaceId) external view virtual override returns (bool) {
        return
            interfaceId == type(ERC1155TokenReceiver).interfaceId ||
            interfaceId == type(ERC721TokenReceiver).interfaceId ||
            interfaceId == type(ERC777TokensRecipient).interfaceId || 
            interfaceId == type(IERC165).interfaceId;
    }
```
QA6. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L115
Zero address check for ``withdrawAddress`` is necessary to avoid losing funding. 

QA7. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L36
``paymasterAndData[20:]`` should be ``paymasterAndData`` instead:

```
(address paymasterId, bytes memory signature) = abi.decode(paymasterAndData, (address, bytes));
```

QA8. https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L21
There is a for loop which contains a low-level call, we need to avoid the msg.value reuse vulnerability problem, see 
the following for more details:
https://blog.trailofbits.com/2021/12/16/detecting-miso-and-opyns-msg-value-reuse-vulnerability-with-slither/
The fix is that we need to make sure this function has no modifier payable.
```
 function multiSend(bytes memory transactions) public{
}

```

