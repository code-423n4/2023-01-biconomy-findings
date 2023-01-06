# Missing event when deployWallet

### Target codebase

[SmartAccountFactory.sol#L53-L61](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L53-L61)

## Impact

The `SmartAccountCreated` event is used only in `deployCounterFactualWallet` but is missing in `deployWallet`

## Tools Used

Manual Analysis

## **Recommended Mitigation Steps**

It is recommended to emit the event int deployWallet function as well.