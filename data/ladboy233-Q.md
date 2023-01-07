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


# Gas payment token suffer from division by zero error if the tokenGasPriceFactor is set to 0

### Line of Code

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L264

### Vulnerability and recommended fix

When handling the gas refund payment in ERC20, the transaction suffer from division by zero error if the tokenGasPriceFactor is set to 0

```solidity
if (refundInfo.gasPrice > 0) {
	//console.log("sent %s", startGas - gasleft());
	// extraGas = gasleft();
	payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);
	emit WalletHandlePayment(txHash, payment);
}
```

which calls:

```solidity
function handlePayment(
	uint256 gasUsed,
	uint256 baseGas,
	uint256 gasPrice,
	uint256 tokenGasPriceFactor,
	address gasToken,
	address payable refundReceiver
) private nonReentrant returns (uint256 payment) {
	// uint256 startGas = gasleft();
	// solhint-disable-next-line avoid-tx-origin
	address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
	if (gasToken == address(0)) {
		// For ETH we will only adjust the gas price to not be higher than the actual used gas price
		payment = (gasUsed + baseGas) * (gasPrice < tx.gasprice ? gasPrice : tx.gasprice);
		(bool success,) = receiver.call{value: payment}("");
		require(success, "BSA011");
	} else {
		payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);
		require(transferToken(gasToken, receiver, payment), "BSA012");
	}
	// uint256 requiredGas = startGas - gasleft();
	//console.log("hp %s", requiredGas);
}
```

note the line:

```solidity
payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);
```

the tokenGasPriceFactor (refundInfo.tokenGasPriceFactor) should not be set to 0.

We recommend the check and validate the tokenGasPriceFactor is not set to 0 before transaction executes.