## Low

### `SmartAccount`: Initialize implementation contract

`SmartAccount` has an unprotected intializer that can be called by anyone to claim ownership of the contract.

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

Since clones are deployed and initialized atomically through `SmartAccountFactory`, this is not an issue, but it's important to initialize the implementation contract itself. If the implementation is left unitialized, anyone can claim ownership and [destroy the contract](https://blog.openzeppelin.com/parity-wallet-hack-reloaded/), destroying any deployed smart accounts.

One simple way to prevent intialization of an implementation using OpenZeppelin `Initializable` is to call `_disableInitializers()` in the constructor:

```solidity
constructor() {
    _disableInitializers();
}
```

Alternatively, initialize the implementation contract yourself as part of deployment. Ideally, do so atomically in a single transaction using a multicall or deployer contract. (Otherwise, there's still a risk that the initialization transaction could be frontrun). Note that the current Hardhat [deployment scripts](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/scripts/wallet-factory.deploy.ts#L36) in this repository **do not** initialize the implementation contract.

### `SmartAccount`: Users can destroy their smart account with `delegatecall`

It's possible for users to destroy their smart wallets by `delegatecall`ing a function that calls `selfdestruct` via [`SmartAccount#execute`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L23).

In a multisig wallet with multiple signers, requireing multiple reviews and approvals for each transaction help detect and mitigate accidentally destructive actions. Since smart accounts have a single signer, the risk of accidentally destroying a wallet is much higher. Make sure any use of `delegatecall` is clearly flagged in the UI, warn or prevent `delegatecall` to unkown contracts, and consider removing this ability altogether.

### `SmartAccount`: Users can overwrite sensitive storage variables with `delegatecall`

Similar to the previous finding, it's possible for users to overwrite important storage variables like `owner`, `_initialized`, and `_entryPoint` by `delegatecall`ing functions on a contract with an overlapping storage layout. Make sure any use of `delegatecall` is clearly flagged in the UI, and consider removing this ability altogether.

## Noncritical

### `SmartAccount`: Missing reentrancy guard initializer

OpenZeppelin `ReentrancyGuardUpgradeable` includes [an initializer](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/25aabd286e002a1526c345c8db259d57bdf0ad28/contracts/security/ReentrancyGuardUpgradeable.sol#L40), but this function is not called in [`SmartAccount#init`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L162-L176):

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

This does not have a security impact, since the reentrancy guard will be correctly applied on the first re-entrant call, but it doesmake the first re-entrant call more expensive.

Suggestion:

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
        __ReentrancyGuard_init();
        owner = _owner;
        _entryPoint =  IEntryPoint(payable(_entryPointAddress));
        if (_handler != address(0)) internalSetFallbackHandler(_handler);
        setupModules(address(0), bytes(""));
    }
```

### `SmartAccountFactory`: Missing `SmartAccountCreated` event in `deployWallet`

`SmartAccountFactory#deployCounterFactualWallet` emits a `SmartAccountCreated` event that logs information about the newly created smart wallet, but `deployWallet` omits this event:

[`SmartAccountFactory#deployWallet`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L53-L61)

```solidity
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

Suggested:

```solidity
    function deployWallet(address _owner, address _entryPoint, address _handler) public returns(address proxy){
        bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
        // solhint-disable-next-line no-inline-assembly
        assembly {
            proxy := create(0x0, add(0x20, deploymentData), mload(deploymentData))
        }
        emit SmartAccountCreated(proxy, _defaultImpl, _owner, VERSION, _index);
        BaseSmartAccount(proxy).init(_owner, _entryPoint, _handler);
        isAccountExist[proxy] = true;
    }
```

Additionally, consider adding `_entrypoint` and `_handler` as parameters to the `SmartAccountCreated` event.

### `SmartAccount`: Prefer `block.chainid` to inline assembly

[`SmartAccount#getChainId`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L168-L177) uses inline assembly to read the current chain ID:

```solidity
    /// @dev Returns the chain id used by this contract.
    function getChainId() public view returns (uint256) {
        uint256 id;
        // solhint-disable-next-line no-inline-assembly
        assembly {
            id := chainid()
        }
        return id;
    }
```

In solidity 0.8.12, the `block` global includes a `chainid` member that returns the current chain ID without the need for inline assembly.

Suggestion:

```solidity
    /// @dev Returns the chain id used by this contract.
    function getChainId() public view returns (uint256) {
        return block.chainid;
    }
```

### `LibAddress`: Prefer `address.code.length` to inline assembly

[`LibAddress#isContract`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol#L11-L16) uses inline assembly to read the codesize of a given address:

```solidity
  function isContract(address account) internal view returns (bool) {
    uint256 csize;
    // solhint-disable-next-line no-inline-assembly
    assembly { csize := extcodesize(account) }
    return csize != 0;
  }
```

In solidity 0.8.12, the `address` type includes a `code` member with a `length` attribute that returns its codesize without the need for inline assembly. (Under the hood this uses the `EXTCODESIZE` opcode directly and is equivalent to the assembly above).

Suggestion:

```solidity
  function isContract(address account) internal view returns (bool) {
    return account.code.length != 0;
  }
```

### `SmartAccount`: Typo in initializer revert string

The revert string for "Invalid Handler" accidentally duplicates the error message for "Invalid Entrypoint."

[`SmartAccount#init`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L162-L176):

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

### `Executor`: Index `to` addresses

The `Executor` contract extends the Gnosis `Executor` contract with additional `ExecutionFailure` and `ExecutionSuccess` events:

[`Executor`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L9-L10)

```solidity
    event ExecutionFailure(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
    event ExecutionSuccess(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
```

Consider indexing the `to` address in these events, to enable filtering for all transactions to a specific destination address.

### `FallbackManager`, `Singleton`, `Proxy`: Inconsistent calculation of storage slots

`FallbackManager`, `Proxy`, and `Singleton` all use a keccak hash to generate a deterministic, unallocated storage slot. However, `FallbackManager` is inconsistent with the other two, since it does not subtract one from the hash value. It's a good practice to subtract 1 from the hash value to ensure that the storage slot is not associated with a known hash preimage. (See [EIP-1967](https://eips.ethereum.org/EIPS/eip-1967) for more).

[`FallbackManager`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L11-L12):

```solidity
    // keccak256("fallback_manager.handler.address")
    bytes32 internal constant FALLBACK_HANDLER_STORAGE_SLOT = 0x6c9a6c4a39284e37ed1cf53d337577d14212a4870fb976a4366c693b939918d5;
```

[`Proxy`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L10-L11):

```solidity
    /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1, and is validated in the constructor */
    bytes32 internal constant _IMPLEMENTATION_SLOT = 0x37722d148fb373b961a84120b6c8d209709b45377878a466db32bbc40d95af26;
```

[`Singleton`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L8-L10):

```solidity
    /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1 */
    bytes32 internal constant _IMPLEMENTATION_SLOT = 0x37722d148fb373b961a84120b6c8d209709b45377878a466db32bbc40d95af26;
```

### `Enum`: Declare enum outside of contract

In Solidity 0.8.12, it's not necessary to declare enums inside a contract. Consider declaring the `Operation` enum outside the contract and importing it wherever it's used, rather than via inheritance.

[`Enum`](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/common/Enum.sol#L4-L7):

```solidity
/// @title Enum - Collection of enums
contract Enum {
    enum Operation {Call, DelegateCall}
}
```

Suggestion:
```solidity
enum Operation {Call, DelegateCall}
```

### `SmartAccountFactory`: `isAccountExist` will return `true` for destroyed wallets

It's possible for the owner of a smart account wallet to intentionally destroy it with a delegatecall that calls `selfdestruct`. In this scenario, `isAccountExist` will return `true` for destroyed smart wallets. It's not clear how this function will be used, but be aware of this possibility.
