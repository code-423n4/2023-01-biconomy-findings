## `executeBatch` will revert if single transaction fails

If an owner or EntryPoint (according to `_requireFromEntryPointOrOwner`)  calls `executeBatch` with one transaction out of a batch that will fail, all other transactions in the batch will fail.

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L469
```
function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
        _requireFromEntryPointOrOwner();
--------
        for (uint i = 0; i < dest.length;) {
            _call(dest[i], 0, func[i]);
--------
        }
    }

    function _call(address target, uint256 value, bytes memory data) internal {
        (bool success, bytes memory result) = target.call{value : value}(data);
        if (!success) {
            assembly {
                revert(add(result, 32), mload(result))
            }
        }
    }
```

## Recommendation

Do not revert if call did not succeed, instead emit a log or store the target and data in storage to be later troubleshooted by owner.

----

## `execute` and `executeBatch` have an onlyOwner modifier

`_requireFromEntryPointOrOwner()` hints that `execute` and `executeBatch` are supposed to be called by an `EntryPoint`.
The current implementation of the function has an `onlyOwner` modifier and therefore cannot be called by the `EntryPoint`.
https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L461
```
function execute(address dest, uint value, bytes calldata func) external onlyOwner{
        _requireFromEntryPointOrOwner();
------
    }

    function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
        _requireFromEntryPointOrOwner();
------
        }
```

## Recommendation

If the functions are supposed to be called by the EntryPoint
- Remove the `onlyOwner` modifier

If the functions are supposed to be called only by the owner:
- Remove the ` _requireFromEntryPointOrOwner();` function call.
---
## Incorrect error message in `init` function

The `init` function reverts if `_handler` is `address(0)`. The revert message is `Invalid Entrypoint` when it should be `Invalid Handler`:

https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L171
```
function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
------
        require(_handler != address(0), "Invalid Entrypoint");
------
        if (_handler != address(0)) internalSetFallbackHandler(_handler);
------
    }
```

## Recommendation

Change the revert message to `Invalid Handler` 
