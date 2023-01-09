* ```ReentrancyGuardUpgradeable``` is not initialized by calling ```__ReentrancyGuard_init``` or ```__ReentrancyGuard_init_unchained```:
```solidity
contract SmartAccount is 
     ...
     ReentrancyGuardUpgradeable
```
https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/security/ReentrancyGuardUpgradeable.sol#L40-L46

* Setting an owner could be separated into a 2-step process to prevent accidental mistakes:
```solidity
  function setOwner(address _newOwner) external mixedAuth
```

* No need for assembly, can use ```block.chainid```:
```solidity
function getChainId() public view returns (uint256) {
   uint256 id;
   // solhint-disable-next-line no-inline-assembly
   assembly {
       id := chainid()
   }
   return id;
}
```

* Should be ```<=```:
```solidity
  require(stake < type(uint112).max, "stake overflow");
```

* Should better use safe casting:
```solidity
  info.deposit = uint112(info.deposit - withdrawAmount);
```

* Probably you are aware that this is not a reliable way to check for EOA:
```solidity
  function isContract(address account) internal view returns (bool) {
    uint256 csize;
    // solhint-disable-next-line no-inline-assembly
    assembly { csize := extcodesize(account) }
    return csize != 0;
  }
```

* ```deployWallet``` similarly like ```deployCounterFactualWallet``` should emit ```SmartAccountCreated``` event.

* ```getModulesPaginated``` does not return the correct ```next```, this function was copied from Gnosis Safe and is fixed in the upcoming release:
https://github.com/safe-global/safe-contracts/issues/461

* Gnosis Safe is not entirely EIP-1271 compliant and are planning to refactor EIP-1271 support:
"EIP-1271 in the form added is not supported anymore, therefore the logic for it should be removed"
See: https://github.com/safe-global/safe-contracts/issues/391 and https://forum.gnosis-safe.io/t/safe-contract-v2/87