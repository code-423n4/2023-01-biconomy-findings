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

### No. 2 Extra ownership check
The modifier `onlyOwner()` only accepts the owner as a sender, so the check `_requireFromEntryPointOrOwner` is redundant.
```
function execute(address dest, uint value, bytes calldata func) external onlyOwner{
        _requireFromEntryPointOrOwner();
        _call(dest, value, func);
    }

    function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
        _requireFromEntryPointOrOwner();
        require(dest.length == func.length, "wrong array lengths");
        for (uint i = 0; i < dest.length;) {
            _call(dest[i], 0, func[i]);
            unchecked {
                ++i;
            }
        }
    }
```
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460