## Summary
### Low Risk
|      | Issue                                                                                       |
|------|---------------------------------------------------------------------------------------------|
| L-01 | `SmartAccountFactory.deployWallet` does not fully comply with EIP-4337                      |
| L-02 | Wrong error string                                                                          |
| L-03 | The "no smart contract" check in `VerifyingSingletonPaymaster.depositFor()` can be bypassed |

### Non critical
|      | Issue                                                                            |
|------|----------------------------------------------------------------------------------|
| NC-01| Typos                                                                            |
| NC-02| Tautologies                                                                      |
| NC-03| Redundant check                                                                    |
| NC-04| Open TODOs                                                                       |
         


## Low

### [L‑01] `SmartAccountFactory.deployWallet` does not fully comply with EIP-4337

This function uses [CREATE](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L57) to deploy the account.

As per [EIP-4337](https://eips.ethereum.org/EIPS/eip-4337#first-time-account-creation):

```
The wallet creation itself is done by a “factory” contract, with wallet-specific data.
The factory is expected to use CREATE2 (not CREATE) to create the wallet, so that the order of creation of wallets doesn’t interfere with the generated addresses
```

Consider removing this function, as `deployCounterFactualWallet()` already allows creation of wallets.

### [L‑02] Wrong error string  

There is a wrong error string in `SmartAccount.init()`. This can be misleading if there is a  zero `_handler` passed to `SmartAccountFactory.deployCounterFactualWallet`, as the error wrongfully states the problem is with `entryPoint`.

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171
```solidity
171:         require(_handler != address(0), "Invalid Entrypoint");
```

This should be:

```diff
-171:         require(_handler != address(0), "Invalid Entrypoint");
+171:         require(_handler != address(0), "Invalid Handler");
```

### [L‑03] The "no smart contract" check in `VerifyingSingletonPaymaster.depositFor()` can be bypassed

`VerifyingSingletonPaymaster.depositFor()` has a check to ensure `paymasterId` is not a smart contract.

This check can be circumvented easily as it checks `account.code.length > 0`:

For instance, by calling `VerifyingSingletonPaymaster.depositFor()` with `paymasterId` as a not-yet-existing contract (ie a contract deployed using `CREATE2`, whose address can be known in advance)

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L49
```
48: function depositFor(address paymasterId) public payable {
49:         require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");
50:         require(paymasterId != address(0), "Paymaster Id can not be zero address");
51:         paymasterIdBalances[paymasterId] += msg.value;
52:         entryPoint.depositTo{value : msg.value}(address(this));
53:     }
```

There is no simple mitigation here, but you can perhaps consider `depositFor()` automatically setting `paymasterId` as `msg.sender`

## Non Critical
### [NC‑01] Typos

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L74
```solidity
74:  * @notice Throws if the sender is not an the owner. //@audit - replace with : Throws if the sender is not the owner
```

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L382
```solidity
382: /// @param targetTxGas Fas that should be used for the safe transaction. //@audit - replace with: Gas that should be used for the safe transaction
```

### [NC‑02] Tautologies

In `SmartAccount`, `execute()` and `executeBatch()` have the `onlyOwner` modifier, meaning they can only be called by the `owner`.

```solidity
76: modifier onlyOwner {
77:         require(msg.sender == owner, "Smart Account:: Sender is not authorized");
78:         _;
79:     }
```

The `_requireFromEntryPointOrOwner()` call is hence redundant and should be removed.

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460-L463
```solidity
460: function execute(address dest, uint value, bytes calldata func) external onlyOwner{
461:         _requireFromEntryPointOrOwner();
462:         _call(dest, value, func);
463:     }
```

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465-L474
```solidity
465: function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
466:         _requireFromEntryPointOrOwner();
467:         require(dest.length == func.length, "wrong array lengths");
468:         for (uint i = 0; i < dest.length;) {
469:             _call(dest[i], 0, func[i]);
470:             unchecked {
471:                 ++i;
472:             }
473:         }
474:     }
```

### [NC‑03] Redundant check

In `SmartAccount.init()`, there is a check that `_handler != address(0)` before calling `internalSetFallbackHandler(_handler)` [here](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L174).

This check is redundant as [a previous check](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171) ensures `_handler != address(0)`

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166-L176
```solidity
166: function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
167:         require(owner == address(0), "Already initialized");
168:         require(address(_entryPoint) == address(0), "Already initialized");
169:         require(_owner != address(0),"Invalid owner");
170:         require(_entryPointAddress != address(0), "Invalid Entrypoint");
171:         require(_handler != address(0), "Invalid Entrypoint");
172:         owner = _owner;
173:         _entryPoint =  IEntryPoint(payable(_entryPointAddress));
174:         if (_handler != address(0)) internalSetFallbackHandler(_handler);
175:         setupModules(address(0), bytes(""));
176:     }
```

Change to 

```diff
-174:         if (_handler != address(0)) internalSetFallbackHandler(_handler);
+174:         internalSetFallbackHandler(_handler);
```

### [NC‑04] Open TODOs

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255
```solidity
255: // TODO: copy logic of gasPrice?
```