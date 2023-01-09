
## Single-step process for critical ownership transfer/renounce is risky
### Impact
Given that `BasePaymaster.sol` is derived from `Ownable`, the ownership management of this contract defaults to `Ownable` ’s `transferOwnership()` and `renounceOwnership()` methods which are not overridden here. 

Such critical address transfer/renouncing in one-step is very risky because it is irrecoverable from any mistakes

Scenario: If an incorrect address, e.g. for which the private key is not known, is used accidentally then it prevents the use of all the `onlyOwner()` functions forever, which includes the changing of various critical addresses and parameters. This use of incorrect address may not even be immediately apparent given that these functions are probably not used immediately. 

When noticed, due to a failing `onlyOwner()` function call, it will force the redeployment of these contracts and require appropriate changes and notifications for switching from the old to new address. This will diminish trust in the protocol and incur a significant reputational damage.

### Code Snippet
https://github.com/code-423n4/2023-01-biconomy/blob/721e2afb493d8bc0bc9488b84eaf2deb14c8b43f/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L16

### Recommended steps
Consider using OZ Ownable2Step library:
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol

Or do it manually. I recommend overriding the inherited methods to null functions and use separate functions for a two-step address change:
1. Approve a new address as a `pendingOwner`
2. A transaction from the `pendingOwner` address claims the pending ownership change.

This mitigates risk because if an incorrect address is used in step (1) then it can be fixed by re-approving the correct address. Only after a correct address is used in step (1) can step (2) happen and complete the address/ownership change.

Also, consider adding a time-delay for such sensitive actions. And at a minimum, use a multisig owner address and not an EOA.


## Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions
### Impact 
For upgradeable contracts, there must be storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments" (quote OpenZeppelin). Otherwise it may be very difficult to write new implementation code. Without storage gap, the variable in child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts, potentially causing loss of user fund or cause the contract to malfunction completely.

See:
https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
For a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

### Proof of Concept
In the following context of the upgradeable contracts they are expected to use gaps for avoiding collision:
- `UUPSUpgradeable`
- `ReentrancyGuardUpgradeable`

However, none of these contracts contain storage gap. The storage gap is essential for upgradeable contract because "It allows us to freely add new state variables in the future without compromising the storage compatibility with existing deployments". Refer to the bottom part of this article:

https://docs.openzeppelin.com/contracts/4.x/upgradeable

If a contract inheriting from a base contract contains additional variable, then the base contract cannot be upgraded to include any additional variable, because it would overwrite the variable declared in its child contract. This greatly limits contract upgradeability.

### Github Permalinks
https://github.com/code-423n4/2023-01-biconomy/blob/7b02ebfcebbf79e6df65ee30efa347cffd28ebcd/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol#L21
`contract SimpleAccount is BaseAccount, UUPSUpgradeable, Initializable {`
https://github.com/code-423n4/2023-01-biconomy/blob/721e2afb493d8bc0bc9488b84eaf2deb14c8b43f/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L28
```
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
### Tools Used
Manual analysis

### Recommended Mitigation Steps
Recommend adding appropriate storage gap at the end of upgradeable contracts such as the below. 

Please reference OpenZeppelin upgradeable contract templates.
`uint256[50] private __gap;`

## Missing checks for `address(0x0)` when assigning values to `address` state or `immutable` variables 
### Summary
Zero address should be checked before assigning it to state or immutable variables
### Github Permalinks
https://github.com/code-423n4/2023-01-biconomy/blob/721e2afb493d8bc0bc9488b84eaf2deb14c8b43f/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L20-L26

https://github.com/code-423n4/2023-01-biconomy/blob/7b02ebfcebbf79e6df65ee30efa347cffd28ebcd/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol#L43-L45

https://github.com/code-423n4/2023-01-biconomy/blob/7b02ebfcebbf79e6df65ee30efa347cffd28ebcd/scw-contracts/contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol#L81-L88

### Mitigation
Check zero address before assigning or using it

