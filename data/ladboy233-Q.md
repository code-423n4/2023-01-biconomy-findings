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