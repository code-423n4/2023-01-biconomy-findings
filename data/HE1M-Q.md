### No. 1 Two-step authentication
Setting the owner should be done through two-step. 
```
function setOwner(address _newOwner) external mixedAuth {
        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
        address oldOwner = owner;
        owner = _newOwner;
        emit EOAChanged(address(this), oldOwner, _newOwner);
    }
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109

Should be:
```
function setPendingOwner(address _newPendingOwner) external mixedAuth {
    require(_newPendingOwner != address(0));
    pendingOwner = _newPendingOwner;
}

function acceptOwnership() external {
    require(msg.sender == pendingOwner);
    owner = msg.sender;
    pendingOwner = address(0);
}
```

### No. 2 Grieving by already deployed smart wallet contract

In the normal scenario, Alice sends fund to her smart wallet (which is not created yet) and then after some time sends the dapp transaction to the SDK. The SDK sends batch of transactions (create wallet and dapp transaction) to the relayer. The relayer first creates the wallet by calling the `SmartAccountFactory.sol`, and then sends the dapp transaction to the created wallet. 

In the malicious scenario, Alice sends fund to her smart wallet (which is not created yet). Then Bob (a malicious user) calls the function `deployCounterFactualWallet` to create a smart wallet for Alice (with the same parameters that are used to predict the Alice's smart wallet address), so the smart wallet is deployed for Alice on the expected address. Then after some time Alice sends the dapp transaction to the SDK. The SDK sends batch of transactions (create wallet and dapp transaction) to the relayer. When the ralayer tries to create the wallet for Alice, it reverts, because it is already created by Bob.

Better to modify as:
```
function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address){
        bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
        bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));

        bytes32 hash = keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, keccak256(deploymentData)));
        address _wallet = address(uint160(uint(hash)));
        if(isAccountExist[_wallet]){
            return _wallet;
        }

        // solhint-disable-next-line no-inline-assembly
        address proxy;
        assembly {
            proxy := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt)
        }
        require(address(proxy) != address(0), "Create2 call failed");
        // EOA + Version tracking
        emit SmartAccountCreated(proxy,_defaultImpl,_owner, VERSION, _index);
        BaseSmartAccount(proxy).init(_owner, _entryPoint, _handler);
        isAccountExist[proxy] = true;
        return proxy;
    }
```