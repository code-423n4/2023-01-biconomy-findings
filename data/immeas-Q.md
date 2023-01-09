# low

## L-1 no validation that the owner is an EOA

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L172

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L112

The documentation specifies that it should be EOAs that are owners but there is no validation done to ensure that.

# non crit

## NC-1 lack of events emitted on state changes

For transparency and trust, important state changes should emit events:

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65-L68

```javascript
File: paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol

65:    function setSigner( address _newVerifyingSigner) external onlyOwner{
66:        require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
67:        verifyingSigner = _newVerifyingSigner;
68:    }
```

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24-L26

```javascript
File: paymasters/BasePaymaster.sol

24:    function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
25:        entryPoint = _entryPoint;
26:    }
```

## NC-2 consider removing `Math.sol` (and `Strings.sol`)

`Math.sol` is mentioned to be in scope but it is not used anywhere (but in `Strings.sol` that is not in scope an also not used anywhere).


Consider removing these libraries as it is confusing with code that is not used.

## NC-3 indentation is off

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L320-L342

Indented with extra 4 spaces

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L85-L93

Indented with just 3 spaces

## NC-4 unnecessary parameter

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L310

```javascript
File: SmartAccount.sol

310:        uint256 i = 0;
311:        address _signer;
312:        (v, r, s) = signatureSplit(signatures, i);
```

Its always `0`, comes from the Gnosis code where it is used to handle multiple owners each providing a signature.

## NC-5 long rows

GitHub wraps after column 164 so its good to keep rows shorter than that

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L46

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L239

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L357

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489

```javascript
File: SmartAccount.sol

46:     //     "AccountTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"

...

239:                payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);

...

357:    ///      Since the `estimateGas` function includes refunds, call this method to get an estimated of the costs that are deducted from the safe with `execTransaction`

...

489:    function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {   

```

## NC-6 Misspelled comments

### `SmartAccount.sol:`

#### `Seperators`
_separators_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L38

```javascript
38:    // Domain Seperators
```

#### substract
_subtract_

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L228

```javascript
228:            // We only substract 2500 (compared to the 3000 before) to ensure that the amount passed is still higher than targetTxGas
```