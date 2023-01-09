## GAS-1: Consider using IR Cogen compiler pipeline for gas optimization
- Description: The IR-based code generator was introduced with an aim to not only allow code generation to be more transparent and auditable but also to enable more powerful optimization passes that span across functions.
- Location: Project Wide
- Proof of Gas savings: Enabling {viaIR: true} in the compiler settings will enable the IR-based code generator. This will allow the compiler to use more powerful optimization passes that span across functions. The gas savings will be in the range of 5-10%.

```bash
$ npx hardhat test
127181000000000000
    ✓ can send transactions and charge wallet for fees in erc20 tokens

·----------------------------------------------------------|---------------------------|-------------|-----------------------------·
|                   Solc version: 0.8.12                   ·  Optimizer enabled: true  ·  Runs: 200  ·  Block limit: 30000000 gas  │
···························································|···························|·············|······························
|  Methods                                                                                                                         │
····························|······························|·············|·············|·············|···············|··············
|  Contract                 ·  Method                      ·  Min        ·  Max        ·  Avg        ·  # calls      ·  usd (avg)  │
····························|······························|·············|·············|·············|···············|··············
|  DepositPaymaster         ·  addDepositFor               ·      66363  ·      83415  ·      72051  ·            3  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  DepositPaymaster         ·  addStake                    ·          -  ·          -  ·      82651  ·            1  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  DepositPaymaster         ·  addToken                    ·          -  ·          -  ·      46832  ·            1  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  EntryPoint               ·  addStake                    ·      31542  ·      68542  ·      44809  ·            3  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  EntryPoint               ·  depositTo                   ·      28863  ·      45963  ·      40259  ·            3  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  EntryPoint               ·  handleAggregatedOps         ·     163341  ·     345337  ·     245824  ·            6  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  EntryPoint               ·  handleOps                   ·     101621  ·     507653  ·     256367  ·           28  ·          -  │
95 passing (2m)

```

```bash
$ npx hardhat test --ir

127771000000000000
    ✓ can send transactions and charge wallet for fees in erc20 tokens

·----------------------------------------------------------|---------------------------|-------------|-----------------------------·
|                   Solc version: 0.8.12                   ·  Optimizer enabled: true  ·  Runs: 200  ·  Block limit: 30000000 gas  │
···························································|···························|·············|······························
|  Methods                                                                                                                         │
····························|······························|·············|·············|·············|···············|··············
|  Contract                 ·  Method                      ·  Min        ·  Max        ·  Avg        ·  # calls      ·  usd (avg)  │
····························|······························|·············|·············|·············|···············|··············
|  DepositPaymaster         ·  addDepositFor               ·      65556  ·      82596  ·      71236  ·            3  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  DepositPaymaster         ·  addStake                    ·          -  ·          -  ·      83106  ·            1  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  DepositPaymaster         ·  addToken                    ·          -  ·          -  ·      46818  ·            1  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  EntryPoint               ·  addStake                    ·      31943  ·      68943  ·      45210  ·            3  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  EntryPoint               ·  depositTo                   ·      28890  ·      45990  ·      40286  ·            3  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  EntryPoint               ·  handleAggregatedOps         ·     161102  ·     296898  ·     225707  ·            6  ·          -  │
····························|······························|·············|·············|·············|···············|··············
|  EntryPoint               ·  handleOps                   ·     100650  ·     406034  ·     212334  ·           28  ·          -  │
·
```


## GAS-2: Use short revert strings.
- Description: Revert strings are stored in the contract's bytecode. The longer the string, the more expensive the deployment. The string is also stored in the transaction receipt, so the longer the string, the more expensive the transaction
- Location: [SmartAccount.sol#L110, L128, L77](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L110) [SmartAccountFactory.sol#L18 ](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L18) [SmartAccountNoAuth.sol#L77, L110, L128](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L77) [MultiSend.sol#L27](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L27),[VerifyingSingletonPaymaster.sol#L49, L50, L66, L107](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L49)
- count: 10

## GAS-3: Upgrade to atleast version 0.8.13.
- Description: Benefits of 0.8.13 include[source](https://blog.soliditylang.org/2021/10/18/solidity-v0.8.13-release-announcement/)
- -Yul IR Pipeline Production Ready: The performance of the new pipeline is not yet always superior to the old one, but it can do much higher-level optimization across functions
-  Memory-Safe Assembly Minimizing Stack Too Deep error The compiler pipeline is that the Yul optimizer can move stack variables to memory and thus avoid the “stack too deep” issue in a lot of cases.

## GAS-4: Declare Constructor as payable.
- Description: You eliminate the payable check in the init code fragment Thereby reducing deployment byte code thus gas during deployment.
- Location: Project Wide (there are 21 constructors without the payable keyword)
- count: 21 in 19 files

## GAS-5: Function modifiers can be inefficient
- Description: The code of modifiers is inlined inside the modified function, thus adding up size and costing gas. Limit the modifiers. Internal functions are not inlined, but called as separate functions. They are slightly more expensive at run time, but save a lot of redundant byte code in deployment, if used more than once
- Location: [SmartAccountNoAuth.sol#L88, L76, L82 ](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L88)[SmartAccount.sol#L88, L76, L82 ](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L88)
- count: 6
- Recommendation: Use internal functions instead of modifiers
```solidity
// Before

   modifier onlyOwner {
        require(msg.sender == owner, "Smart Account:: Sender is not authorized");
        _;
    }
```
```solidity
// after

    modifier onlyOwner() {
        _onlyOwner();
        _;
    }

    function _onlyOwner() internal view {
        require(msg.sender == owner, "Smart Account:: Sender is not authorized");
    }
```
## GAS-6: User smaller types and Pack your variables!
- Description: Much smaller types can be used in the DepositInfo struct such that it can be packed into a single slot instead of 2 slots. This will save gas on SLOAD and SSTORE operations.
- Location: [IStakeManager.sol#L53](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L53)
- Count: 1
- Example: 
```solidity
// Before Occupies 2 slot
    struct DepositInfo {
        uint112 deposit;
        bool staked;
        uint112 stake;
        uint32 unstakeDelaySec;
        uint64 withdrawTime;
    }
```
```solidity
// After Occupies 1 slot
    struct DepositInfo {
        uint72 deposit;
        bool staked;
        uint72 stake;
        uint32 unstakeDelaySec;
        uint64 withdrawTime;
    }
```
## GAS-7: Use bytes32 rather string/bytes
- Description: Fixed sizes are always cheaper. It appears if you used bytes32 rather than string even on constants variables saves on deployment cost
- Location: [SmartAccount.sol#L36](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L36)  [SmartAccountFactory.sol#L11](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L11) [DefaultCallbackHandler.sol#L12, L13](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L12)
- Count: 5 
- Example: 
```solidity
// Before
    string public constant NAME = "Smart Account";
```
```solidity
// After
    bytes32 public constant NAME = "Smart Account";
```
## GAS-8: Avoid redundant operations
- Description: The parameter `_handler` is being checked twice in the init() function of SmartAccount.sol and SmartAccountNoAuth.sol. This is redundant and can be removed. 
- Location: [SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166)
- Count: 1
- Sample Code:
```solidity

function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
        require(owner == address(0), "Already initialized");
        require(address(_entryPoint) == address(0), "Already initialized");
        require(_owner != address(0),"Invalid owner");
        require(_entryPointAddress != address(0), "Invalid Entrypoint");

// -handler is being checked here that it's not a zero address
        require(_handler != address(0), "Invalid Entrypoint");
        owner = _owner;
        _entryPoint =  IEntryPoint(payable(_entryPointAddress));

// -handler is being checked again here that it's not a zero address redundant
        if (_handler != address(0)) internalSetFallbackHandler(_handler);
        setupModules(address(0), bytes(""));
    }
```
## GAS-9: Eliminated code with no effects
- Description: Code with no effects only adds to the deployed byte code and adds to deployment costs. The function `tokenReceived()` is not being used
- Location: [DefaultCallbackHandler.sol#L44](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L44) 
- Count: 1
