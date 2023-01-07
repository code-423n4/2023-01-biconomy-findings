# ReentrancyGuardUpgradeable is not initalized in the init function of the SmartAccount

### Line of Code

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L28

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166

### Vulnerability and recommended fix

The SmartAccount inherits from ReentrancyGuardUpgradeable

```solidity
contract SmartAccount is 
     Singleton,
     BaseSmartAccount,
     IERC165,
     ModuleManager,
     SignatureDecoder,
     SecuredTokenTransfer,
     ISignatureValidatorConstants,
     FallbackManager,
     Initializable,
     ReentrancyGuardUpgradeable
    {
```

However, in the init function of the SmartAccount, the ReentrancyGuardUpgradeable is not initalized.

```solidity
    // init
    // Initialize / Setup
    // Used to setup
    // i. owner ii. entry point address iii. handler
    function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
        require(owner == address(0), "Already initialized");
        require(address(_entryPoint) == address(0), "Already initialized");
        require(_owner != address(0),"Invalid owner");
        require(_entryPointAddress != address(0), "Invalid Entrypoint");
        require(_handler != address(0), "Invalid Entrypoint");
        owner = _owner;
        _entryPoint =  IEntryPoint(payable(_entryPointAddress));
        if (_handler != address(0)) internalSetFallbackHandler(_handler);
        setupModules(address(0), bytes(""));
    }
```

If in fact, if the SmartAccount is not upgradeable, no need to use the ReentrancyGuardUpgradeable, just use the regular reentrancy guard should be sufficient, otherwise, the recommended fix is init the ReentrancyGuardUpgradeable inside the init function

https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/25aabd286e002a1526c345c8db259d57bdf0ad28/contracts/security/ReentrancyGuardUpgradeable.sol#L40

```solidity
function __ReentrancyGuard_init() internal onlyInitializing {
	__ReentrancyGuard_init_unchained();
}
```


# front-runnable wallet deployment.

### Line of Code

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L33

### Vulnerability and recommended fix

The function below is front-runnable.

```solidity
function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address proxy){
	bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
	bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
	// solhint-disable-next-line no-inline-assembly
	assembly {
		proxy := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt)
	}
	require(address(proxy) != address(0), "Create2 call failed");
	// EOA + Version tracking
	emit SmartAccountCreated(proxy,_defaultImpl,_owner, VERSION, _index);
	BaseSmartAccount(proxy).init(_owner, _entryPoint, _handler);
	isAccountExist[proxy] = true;
}
```

When the transaction is pending in the mempool, a user can decode the transaction and get the deploymentData and the salt, then deploy the wallet with higher gas fee.

The issue is that the deployCounterFactualWallet can revert and isAccountExist[proxy] will not be correctedly updated.

We recommend the protocol validate the signature to make sure the msg.sender match the owner when deploy the wallet to avoid front-running.