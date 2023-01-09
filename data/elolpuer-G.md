## contracts\smart-contract-wallet\SmartAccount.sol

[Link to function in github.com](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460)

### Description

There is no point in the ```onlyOwner``` modifier, because we check the owner in ```_requireFromEntryPointOrOwner()```.

### Code w/o onlyOwner

```

function execute(address dest, uint value, bytes calldata func) external {
    _requireFromEntryPointOrOwner();
    _call(dest, value, func);
}

function executeBatch(address[] calldata dest, bytes[] calldata func) external {
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

