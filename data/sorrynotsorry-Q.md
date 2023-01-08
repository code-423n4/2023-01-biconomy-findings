# QA (LOW & NON-CRITICAL)

## [L-01] Wrong visibility in SmartAccount contract 
SmartAccount contract implements `mixedAuth` modifier to validate the caller is the `owner` OR the contract itself. The functions implementing this modifier have `external` visibility which the contract itself can't call due to visibility. Recommend changing the `external` visibility to `public` visibility.

Modifier;
```solidity
    modifier mixedAuth {
        require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
        _;
   }
```
[Link](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L82-L85)

Instances where the modifier is called in external visibility;

[setOwner()](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109)
[UpdateImplementation()](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L120)
[UpdateEntryPoint()](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L127)

## [L-02] SmartAccountFactory's frontrunnable functions
SmartAccountFactory has `deployCounterFactualWallet` and `deployWallet` functions to deploy SCW using create2/create and pointing it to `_defaultImpl` address. However, these functions are prone to be frontrunned by an actor. 
### Impact
An actor who monitors the mempool for this func-sig can frontrun it and set the parameters `_entryPointAddress`, `_handler` to arbitrary addresses while the `_owner` address is the frontrunned address. 
Since these functions also initialize the proxy and finalizes the SWC setup, it will not be possible to re-create a SWC with the same calling EOA. And if the caller is a dApp/smart contract that does not implement an upgradibility mechanism, it will not be possible to onboard again. 
### Recommended Mitigation Steps
The team might consider removing the `_owner` parameter and using `msg.sender` in function body.
### Proof of Concept
`deployCounterFactualWallet` function below;
```solidity
    function deployCounterFactualWallet(address _owner, address _entryPoint, address _handler, uint _index) public returns(address proxy){
        bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index))));
        bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
        // solhint-disable-next-line no-inline-assembly
        assembly {
            proxy := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt)
        }
        require(address(proxy) != address(0), "Create2 call failed");
        // EOA + Version tracking
        emit SmartAccountCreated(proxy,_defaultImpl,_owner, VERSION, _index);
        BaseSmartAccount(proxy).init(_owner, _entryPoint, _handler);
        isAccountExist[proxy] = true;
    }
```
[Link](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L33-L45)

`deployWallet` function below;
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
[Link](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L53-L61)

Wallet initialization;

```solidity
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
[Link](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166-L176)

## [L-03] Missing sig.length check

SignatureDecorder contract's `signatureSplit` function does not check the signature length as `require(signatures.length == 65)`.

## [NC-01] Hardcoded function signature for external calls
SecuredTokenTransfer contract is used to handle the payments to the relayers in form of gas tokens which are stable tokens as of now. The function signature is hardcoded as `0xa9059cbb` which represents keccak("transfer(address,uint256)"). The team should check the conformity of future gastokens' function signatures when it's intended to add.
