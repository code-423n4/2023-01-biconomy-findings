# Replace `getChainId()` with `block.chainid`
We can get the chainId using `block.chainid` instead of using `getChainId()`.

*Instances (2)*:
```solidity
File: contracts/smart-contract-wallet/SmartAccount.sol
Line 135-147

    function domainSeparator() public view returns (bytes32) {
        return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
    }

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
[Link to code](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

```solidity
File: contracts/smart-contract-wallet/SmartAccountNoAuth.sol
Line 135-147

    function domainSeparator() public view returns (bytes32) {
        return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this));
    }

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
[Link to code](https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol)

We also could delete the `getChainId` function to reduce the deployment cost, since it will never get used.