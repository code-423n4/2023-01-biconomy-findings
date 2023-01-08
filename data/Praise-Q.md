## in function deployWallet() in SmartAccountFactory.sol Lc 53, there are no zero-address checks for this parameters. address _owner, address _entryPoint, address _handler, invalid wallet addresses can be inputed and deployed.
```

 function deployWallet(address _owner, address _entryPoint, address _handler) public returns(address proxy){ 
        bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
        // solhint-disable-next-line no-inline-assembly
        assembly {
            proxy := create(0x0, add(0x20, deploymentData), mload(deploymentData))
        }
        BaseSmartAccount(proxy).init(_owner, _entryPoint, _handler);
        isAccountExist[proxy] = true;
    }
```
## Also, 
iIn BasePayMaster.sol, Lc 67 the public function withdrawTo() has no Zero-address check. funds can be withdrawn to an invalid address.

```
    function withdrawTo(address payable withdrawAddress, uint256 amount) public virtual onlyOwner {
        entryPoint.withdrawTo(withdrawAddress, amount);
    }
```

## In Lc 9, The external function withdrawStake() has no zero- Address check also.

```
    function withdrawStake(address payable withdrawAddress) external onlyOwner {
        entryPoint.withdrawStake(withdrawAddress);
}
```
## In Executor.sol, Lc 14,  the internal function execute() has no zero-address check for "address to" parameter  

```
    function execute(
        address to,
        uint256 value,
        bytes memory data,
        Enum.Operation operation,
        uint256 txGas
    ) internal returns (bool success) {
        if (operation == Enum.Operation.DelegateCall) {
            // solhint-disable-next-line no-inline-assembly
            assembly {
                success := delegatecall(txGas, to, add(data, 0x20), mload(data), 0, 0)
            }
        } else {
            // solhint-disable-next-line no-inline-assembly
            assembly {
                success := call(txGas, to, value, add(data, 0x20), mload(data), 0, 0)
            }
        }
        // Emit events here..
        if (success) emit ExecutionSuccess(to, value, data, operation, txGas);
        else emit ExecutionFailure(to, value, data, operation, txGas);
    }
```