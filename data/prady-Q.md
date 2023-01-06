## [L-01] SIGNATURE LENGTH VERIFICATION MISSING

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L196

The `execTransaction` does not verify that the parameter `signature` length is a valid signature length or not, as the signature param consists of 32 bytes `s` and 32 bytes `r` and 1 byte of `v,` which means the signature length should be equal to 65. 

### Recommended mitigation step

Add this before line [SmartAccount-L198](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L198)
```solidity
contracts/smart-contract-wallet/SmartAccount: 
    require(_signature.length == 65, "invalid signature length");
```

## [L-02] INIT() FUNCTION CAN BE CALLED BY ANYBODY
The `init()` function can be called by anybody when the contract is not initialized.
```solidity
    function init(
        address _owner,
        address _entryPointAddress,
        address _handler
    ) public override initializer {
        require(owner == address(0), "Already initialized");
        require(address(_entryPoint) == address(0), "Already initialized");
        require(_owner != address(0), "Invalid owner");
        require(_entryPointAddress != address(0), "Invalid Entrypoint");
        require(_handler != address(0), "Invalid Entrypoint");
        owner = _owner;
        _entryPoint = IEntryPoint(payable(_entryPointAddress));
        if (_handler != address(0)) internalSetFallbackHandler(_handler);
        setupModules(address(0), bytes(""));
    }
```
More importantly, if someone else runs this function, they will have full authority as it sets the `owner` variable. 

### Recommended mitigation step 
Add a check that `init()` can only be called by the deployer. 
```solidity
address internal immutable deployer;
constructor() {
       deployer = msg.sender;
}
...

function init(...) {
      if (msg.sender != DEPLOYER_ADDRESS) { revert NotDeployer(); }
      ... // rest same
} 
```
## [N-01] NOT USING THE LATEST VERSION OF OPENZEPPELIN FROM DEPENDENCIES
The package.json configuration file says that the project is using4.7.3 of OpenZeppelin, which is not the [latest version](https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.8.0)
```js
package.json
    "@openzeppelin/contracts": "^4.7.3",
    "@openzeppelin/contracts-upgradeable": "^4.7.3",
```

## [N-02] STORE CHAIN_ID AS A IMMUTABLE VARIABLE
Instead of calling `getChainId()` function to get the current chain id, use an immutable variable to store the chain-id into the bytecode. 
```solidity
unit immutable public chainId;
init(...) {
        assembly {
            chainId := chainid()
        }
}
```

## [N-03] UNNECESSARY MEMORY PARAMS
Line [SmartAccount.sol#L310](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L310) defines variable `i` to pass to function [`checkSignature`](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L312), instead constant value can be used as variable `i` remains unchanged. 

```solidity
contracts/smart-contract-wallet/SmartAccount.sol/checkSignatures():
          (v, r, s) = signatureSplit(signatures, 0);
```

## [N-04] MISSING EVENTS FOR CRITICAL OPERATIONS
Several critical operations do not trigger events. As a result, it isn't easy to check the behavior of the contracts. Without events, users and blockchain-monitoring systems cannot easily detect suspicious behavior. Ideally, the following critical operations should trigger events:

- [`addDeposit()`](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L525)
- [`withdrawDepositTo()`](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L536)
- [`transfer()`](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449)
- [`pullTokens() `](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455)
- [`execute() `](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460)
- [`executeBatch() `](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465)
- [`execFromEntryPoint ()`](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489)

## [N-05] LINES ARE TOO LONG
Usually, lines in source code are limited to 80 characters.
Reference:
https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
```solidity
contracts/smart-contract-wallet/SmartAccount: 
    function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {   
```