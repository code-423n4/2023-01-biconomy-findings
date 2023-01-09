Function innerHandleOp is external but requires it be called from the contract entry point contract
```solidity
    function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost) {
        uint256 preGas = gasleft();
        require(msg.sender == address(this), "AA92 internal call only");
```
consider changing the function to an internal function