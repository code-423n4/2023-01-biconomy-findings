### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | With assembly, `.call (bool success)` transfer can be done gas-optimized | 8 |
| [G-02] |Remove the `initializer` modifier| 1 |
| [G-03] |Structs can be packed into fewer storage slots |2 |
| [G-04] | `DepositInfo` and `PaymasterData` structs can be rearranged  | 2 |
| [G-05] | Duplicated require()/if() checks should be refactored to a modifier or function |5|
| [G-06] |Can be removed to 'assert' in function '_setImplementation'  | 1 |
| [G-07] |Instead of `emit ExecutionSuccess` and `emit ExecutionFailure` a single ` emit Execution` is gas efficient | 1 |
| [G-08] | Unnecessary computation | 1 |
| [G-09] | Using delete instead of setting `info` struct to 0 saves gas  | 3 |
| [G-10] | Empty blocks should be removed or emit something  | 1 |
| [G-11] | Using `storage` instead of `memory` for `structs/arrays` saves gas  |11 |
| [G-12] |Use Shift Right/Left instead of Division/Multiplication  | 3 |
| [G-13] | Use constants instead of type(uintx).max  | 3 |
| [G-14] |Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement  | 2 |
| [G-15] |Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead  | 8 |
| [G-16] |Reduce the size of error messages (Long revert Strings) | 13 |
| [G-17] |Use ``double require`` instead of using ``&&``  | 3 |
| [G-18] |Use nested if and, avoid multiple check combinations |5 |
| [G-19] |Functions guaranteed to revert_ when callled by normal users can be marked `payable`  | 18 |
| [G-20] |Setting the _constructor_ to `payable` | 4 |
| [G-21] |Use ``assembly`` to write _address storage values_  | 8 |
| [G-22] |++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops| 6 |
| [G-23] |Sort Solidity operations using short-circuit mode| 8 |
| [G-24] |`x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables  | 3 |
| [G-25] |Use a more recent version of solidity | 36 |
| [G-26] |Optimize names to save gas |  |
| [G-27] |Upgrade Solidity's optimizer  |  |

Total 27 issues

### [G-01] With assembly, `.call (bool success)` transfer can be done gas-optimized

`return` data `(bool success,)` has to be stored due to EVM architecture, but in a usage like below, 'out' and 'outsize' values are given (0,0), this storage disappears and gas optimization is provided.

https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19

There are 8 instances of the topic.

```diff
contracts\smart-contract-wallet\SmartAccount.sol#l451
  449     function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
- 451:       (bool success,) = dest.call{value:amount}("");
+            bool success;                                 
+            assembly {                                    
+                success := call(gas(), dest, amount, 0, 0)
+            }                                             
+                                                          
  452            require(success,"transfer failed");
  453       }
  454
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L451

```solidity
contracts\smart-contract-wallet\SmartAccount.sol:
247     function handlePayment(
261:        (bool success,) = receiver.call{value: payment}("");
262         require(success, "BSA011");
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L261

```solidity
  271     function handlePaymentRevert(
  285:             (bool success,) = receiver.call{value: payment}("");
  286             require(success, "BSA011");
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L285

```solidity
contracts\smart-contract-wallet\SmartAccount.sol:
  525     function addDeposit() public payable {
  526 
  527:         (bool req,) = address(entryPoint()).call{value : msg.value}("");
  528         require(req);
  529     }
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L527

```solidity
contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol:
  35     function _compensate(address payable beneficiary, uint256 amount) internal {
  36         require(beneficiary != address(0), "AA90 invalid beneficiary");
  37:         (bool success,) = beneficiary.call{value : amount}("");
  38         require(success, "AA91 failed send to beneficiary");
  39     }
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L37

```solidity
contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol:
   96     function withdrawStake(address payable withdrawAddress) external {
  106:         (bool success,) = withdrawAddress.call{value : stake}("");
  107         require(success, "failed to withdraw stake");
  108     }
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L106

```solidity
contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol:
  115     function withdrawTo(address payable withdrawAddress, uint256 withdrawAmount) external {
  120:         (bool success,) = withdrawAddress.call{value : withdrawAmount}("");
  121         require(success, "failed to withdraw");
  122     }
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L120

```solidity
contracts/smart-contract-wallet/BaseSmartAccount.sol:
  106     function _payPrefund(uint256 missingAccountFunds) internal virtual {
  108:             (bool success,) = payable(msg.sender).call{value : missingAccountFunds, gas : type(uint256).max}("");
  109             (success);
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L108

### [G-02] Remove the `initializer` modifier

if we can just ensure that the `initialize()` function could only be called from within the constructor, we shouldn't need to worry about it getting called again. 

```solidity
contracts/smart-contract-wallet/SmartAccount.sol:
  166:     function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 

```

In the EVM, the constructor's job is actually to return the bytecode that will live at the contract's address. So, while inside a constructor, your address `(address(this))` will be the deployment address, but there will be no bytecode at that address! So if we check `address(this).code.length` before the constructor has finished, even from within a delegatecall, we will get 0. So now let's update our `initialize()` function to only run if we are inside a constructor:

```diff

contracts/smart-contract-wallet/SmartAccount.sol:
  166:     function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
+            require(address(this).code.length == 0, 'not in constructor');

```

Now the Proxy contract's constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero. 

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version. Pretty neat!


### [G-03] Structs can be packed into fewer storage slots

The `UserOperation` struct can be packed into one slot less slot as suggested below.
```diff
scw-contracts\contracts\smart-contract-wallet\aa-4337\interfaces\UserOperation.sol:
  19:     struct UserOperation {
  20 
  21         address sender;                // slot0   (20 bytes)
- 22         uint256 nonce;                                      
+ 22         uint96 nonce;                  // slot0   (12 bytes)
- 23         bytes initCode;                                     
- 24         bytes callData;                                     
- 25         uint256 callGasLimit;                               
- 26         uint256 verificationGasLimit;                       
- 27         uint256 preVerificationGas;                         
  28         uint128 maxFeePerGas;          // slot1   (16 bytes)
  29         uint128 maxPriorityFeePerGas;  // slot1   (16 bytes)
+ 25         uint256 callGasLimit;          // slot2   (32 bytes)
+ 26         uint256 verificationGasLimit;  // slot3   (32 bytes)
+ 27         uint256 preVerificationGas;    // slot4   (32 bytes)
+ 23         bytes initCode;                // slot5   (32 bytes)
+ 24         bytes callData;                // slot6   (32 bytes)
  30         bytes paymasterAndData;        // slot7   (32 bytes)
  31         bytes signature;               // slot8   (32 bytes)
  32     } 
```

The `MemoryUserOp` struct can be packed into one slot less slot as suggested below.
```diff
scw-contracts\contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol:
  144      //a memory copy of UserOp fields (except that dynamic byte arrays: callData, initCode and signature
  145:     struct MemoryUserOp {
  146         address sender;               // slot0
- 147         uint256 nonce;                        
+ 147         uint96 nonce;                 // slot0
  148         uint256 callGasLimit;         // slot1        
  149         uint256 verificationGasLimit; // slot2
  150         uint256 preVerificationGas;   // slot3
  151         address paymaster;            // slot4
- 152         uint256 maxFeePerGas;                 
+ 152         uint128 maxFeePerGas;         // slot5
- 153         uint256 maxPriorityFeePerGas;         
+ 153         uint128 maxPriorityFeePerGas; // slot5
  154     }
```

### [G-04] `DepositInfo` and `PaymasterData` structs can be rearranged

Gas saving can be achieved by updating the ``DepositInfo`` struct as below.
```diff
scw-contracts\contracts\smart-contract-wallet\aa-4337\interfaces\IStakeManager.sol:
  53:     struct DepositInfo {   
+             StakeInfo stakeInfo;   
  54          uint112 deposit;   
  55          bool staked;   
- 56          uint112 stakes;        
- 57          uint32 unstakeDelaySec;
  58          uint64 withdrawTime;   
  59     }   
```

```solidity
 62:     struct StakeInfo {   
 63         uint256 stakes;   
 64         uint256 unstakeDelaySec;   
 65     }
```

Gas saving can be achieved by updating the ``PaymasterData`` struct as below.
```diff
scw-contracts\contracts\smart-contract-wallet\paymasters\PaymasterHelpers.sol:
  7:    struct PaymasterData {
+          PaymasterContext paymasterContex;
- 8:       address paymasterId;             
  9:       bytes signature;
 10:       uint256 signatureLength;
 11: }
```  
``` solidity
  13: struct PaymasterContext {
  14:     address paymasterId;
  15:     //@review
  16: }
```

### [G-05] Duplicated require()/if() checks should be refactored to a modifier or function

```solidity
contracts\smart-contract-wallet\SmartAccount.sol:
  258:    if (gasToken == address(0)) {
  282:    if (gasToken == address(0)) {
```
[SmartAccount.sol#L258](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L258), [SmartAccount.sol#L282](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L282)

```solidity
contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol:
  315:    if (paymaster == address(0)) {
  330:    if (paymaster == address(0)) {
  448:    if (paymaster == address(0)) {

  321:    if (_deadline != 0 && _deadline < block.timestamp) {
  365:    if (_deadline != 0 && _deadline < block.timestamp) {
  
  488:    if (maxFeePerGas == maxPriorityFeePerGas) {
```
[EntryPoint.sol#L315](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L315), [EntryPoint.sol#L330](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L330), [EntryPoint.sol#L448](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L448)

[EntryPoint.sol#L321](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L321), [EntryPoint.sol#L365](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L365)

[EntryPoint.sol#L448](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L448)

```solidity
contracts\smart-contract-wallet\aa-4337\interfaces\UserOperation.sol:
  49:     if (maxFeePerGas == maxPriorityFeePerGas) {
```
[UserOperation.sol#L49](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L49)


```solidity
contracts\smart-contract-wallet\base\ModuleManager.sol:
  34:         require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
  49:         require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
```
[ModuleManager.sol#L34](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34), [ModuleManager.sol#L49](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49)


**Recommendation:**

You can consider adding a modifier like below.

```solidity
 modifer check (address checkToAddress) {
        require(checkToAddress != address(0) && checkToAddress != SENTINEL_MODULES, "BSA101");
        _;
    }
```

Here are the data available in the covered contracts. The use of this situation in contracts that are not covered will also provide gas optimization.

### [G-06] Can be removed to 'assert' in function '_setImplementation'

The state variable _IMPLEMENTATION_SLOT constant is precomputed ``keccak256("biconomy.scw.proxy.implementation") - 1``. The assert check here is unnecessary. Removing this control provides gas optimization.

```diff
contracts\smart-contract-wallet\common\Singleton.sol:
  12     function _setImplementation(address _imp) internal {
- 13:         assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
  14         // solhint-disable-next-line no-inline-assembly
  15         assembly {
  16           sstore(_IMPLEMENTATION_SLOT, _imp)
  17          }
  18     }
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13

**Proof of Concept:**
The optimizer was turned on and set to 200 runs test was done in 0.8.12

```solidity
contract GasTest is DSTest {
    
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        c0._setImplementation(0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2);
        c1._setImplementation(0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2);
    }
}

contract Contract0 {
    // singleton slot always needs to be first declared variable, to ensure that it is at the same location as in the Proxy contract.

    /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1 */
    bytes32 internal constant _IMPLEMENTATION_SLOT = 0x37722d148fb373b961a84120b6c8d209709b45377878a466db32bbc40d95af26;

    function _setImplementation(address _imp) public {
        assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));
        // solhint-disable-next-line no-inline-assembly
        assembly {
          sstore(_IMPLEMENTATION_SLOT, _imp)
         }
    }
}

contract Contract1 {
    // singleton slot always needs to be first declared variable, to ensure that it is at the same location as in the Proxy contract.

    /* This is the keccak-256 hash of "biconomy.scw.proxy.implementation" subtracted by 1 */
    bytes32 internal constant _IMPLEMENTATION_SLOT = 0x37722d148fb373b961a84120b6c8d209709b45377878a466db32bbc40d95af26;
    
    function _setImplementation(address _imp) public {
        
        assembly {
            sstore(_IMPLEMENTATION_SLOT, _imp)
        }
    }
}
```
**Gas Report:**
```js
╭──────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/test.sol:Contract0 contract ┆                 ┆       ┆        ┆       ┆         │
╞══════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 71123                                ┆ 387             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ _setImplementation                   ┆ 22435           ┆ 22435 ┆ 22435  ┆ 22435 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
╭──────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/test.sol:Contract1 contract ┆                 ┆       ┆        ┆       ┆         │
╞══════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 38893                                ┆ 225             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ _setImplementation                   ┆ 22336           ┆ 22336 ┆ 22336  ┆ 22336 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
```

### [G-07] Instead of `emit ExecutionSuccess` and `emit ExecutionFailure` a single ` emit Execution` is gas efficient

If the `emit ExecutionSuccess` and `emit ExecutionFailure` at the end of the `execute` function are removed and arranged as follows, gas savings will be achieved. The last element of the `event Execution` bool success will indicate whether the operation was successful or unsuccessful, with a value of true or false.

```diff
contracts\smart-contract-wallet\base\Executor.sol:
- event ExecutionFailure(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
- event ExecutionSuccess(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas);
+ event Execution(address to, uint256 value, bytes data, Enum.Operation operation, uint256 txGas, bool success);

  13     function execute(
  31         // Emit events here..
- 32:         if (success) emit ExecutionSuccess(to, value, data, operation, txGas);
- 33:         else emit ExecutionFailure(to, value, data, operation, txGas);
+             emit Execution (to, value, data, operation, txGas, success);
  34     }
  35     
  36 }
```

### [G-08] Unnecessary computation

When emitting an event that includes a new and an old value, it is cheaper in gas to avoid caching the old value in memory. Instead, emit the event, then save the new value in storage.

```diff
contracts\smart-contract-wallet\SmartAccount.sol:
  111         address oldOwner = owner;
+ 113:        emit EOAChanged(address(this), oldOwner, _newOwner);
  112         owner = _newOwner;
- 113:        emit EOAChanged(address(this), oldOwner, _newOwner);
  114     }
```
[SmartAccount.sol#L468](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L113)


### [G-09] Using delete instead of setting `info` struct to 0 saves gas

```solidity
contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol:
   96     function withdrawStake(address payable withdrawAddress) external {
   97         DepositInfo storage info = deposits[msg.sender];
  102:         info.unstakeDelaySec = 0;
  103:         info.withdrawTime = 0;
  104:         info.stake = 0;
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L102-L104

### [G-10] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

```solidity
contracts/smart-contract-wallet/SmartAccount.sol:
  550:     receive() external payable {}
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550

### [G-11] Using `storage` instead of `memory` for `structs/arrays` saves gas

When fetching data from a storage location, assigning the data to a ``memory`` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the ``memory`` keyword, declaring the variable with the ``storage`` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a ``memory`` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires ``memory``, or if the array/struct is being read from another ``memory`` array/struct

11 results - 2 files:
```solidity
contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol:
  171:     MemoryUserOp memory mUserOp = opInfo.mUserOp;

  229:     UserOpInfo memory outOpInfo;

  234:     StakeInfo memory paymasterInfo = getStakeInfo(outOpInfo.mUserOp.paymaster);

  235:     StakeInfo memory senderInfo = getStakeInfo(outOpInfo.mUserOp.sender);

  238:     StakeInfo memory factoryInfo = getStakeInfo(factory);

  241:     AggregatorStakeInfo memory aggregatorInfo = AggregatorStakeInfo(aggregator, getStakeInfo(aggregator));

  293:     MemoryUserOp memory mUserOp = opInfo.mUserOp;

  351:     MemoryUserOp memory mUserOp = opInfo.mUserOp;

  389:     MemoryUserOp memory mUserOp = outOpInfo.mUserOp;

  444:     MemoryUserOp memory mUserOp = opInfo.mUserOp;
```
[EntryPoint.sol#L171](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L171), [EntryPoint.sol#L229](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L229), [EntryPoint.sol#L234](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L234), [EntryPoint.sol#L235](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L235), [EntryPoint.sol#L238](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L238), [EntryPoint.sol#L241](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L241), [EntryPoint.sol#L293](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L293), [EntryPoint.sol#L351](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L351), [EntryPoint.sol#L389](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L389), [EntryPoint.sol#L444](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L444)

```solidity
contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol:
  126:     PaymasterContext memory data = context.decodePaymasterContext();
```
[VerifyingSingletonPaymaster.sol#L126](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L126)


### [G-12] Use Shift Right/Left instead of Division/Multiplication

A division/multiplication by any number x being a power of 2 can be calculated by shifting to the right/left. While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. 

Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

3 results 2 files:
```diff
contracts/smart-contract-wallet/libs/Math.sol:
- 36:        return (a & b) + (a ^ b) / 2;
+ 36:        return (a & b) + (a ^ b) >> 1;
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L36


```diff
contracts/smart-contract-wallet/SmartAccount.sol:
- 200:      uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
+ 200:      uint256 startGas = gasleft() + 21000 + msg.data.length << 3;
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L200


```diff
- 224:       require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
+ 224:       require(gasleft() >= max((_tx.targetTxGas << 6) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L224


### [G-13] Use constants instead of type(uintx).max

type(uint120).max or type(uint112).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.

3 results - 2 files:

```solidity
contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol:
  397:       require(maxGasValues <= type(uint120).max, "AA94 gas values overflow");
```
[EntryPoint.sol#L397](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L397)

```solidity
contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol:
  41:         require(newAmount <= type(uint112).max, "deposit overflow");

  65:         require(stake < type(uint112).max, "stake overflow");
```
[StakeManager.sol#L41](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L41), [StakeManager.sol#L65](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L65)


### [G-14] Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement
```
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a } 
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
```
This will stop the check for overflow and underflow so it will save gas.

2 results - 2 files:

```solidity
contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol:
  116         DepositInfo storage info = deposits[msg.sender];
  117         require(withdrawAmount <= info.deposit, "Withdraw amount too large");
  118:         info.deposit = uint112(info.deposit - withdrawAmount);
```
[StakeManager.sol#L118](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L118)

```solidity
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:
  56        uint256 currentBalance = paymasterIdBalances[msg.sender];
  57        require(amount <= currentBalance, "Insufficient amount to withdraw");
  58:       paymasterIdBalances[msg.sender] -= amount;
 ```
[VerifyingSingletonPaymaster.sol#L58](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L58)


### [G-15] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contracts gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 

Use a larger size then downcast where needed.

8 results - 3 files:
```solidity
contracts\smart-contract-wallet\aa-4337\core\StakeManager.sol:
   42:   info.deposit = uint112(newAmount);

   59:   function addStake(uint32 _unstakeDelaySec) public payable {

   69:   uint112(stake),

   84:   uint64 withdrawTime = uint64(block.timestamp) + info.unstakeDelaySec;

  118:  info.deposit = uint112(info.deposit - withdrawAmount);
```
[StakeManager.sol#L42](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L42), [StakeManager.sol#L59](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L59), [StakeManager.sol#L69](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L69), [StakeManager.sol#L84](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L84), [StakeManager.sol#L118](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L118)

```solidity
contracts\smart-contract-wallet\paymasters\BasePaymaster.sol:
   75:   function addStake(uint32 unstakeDelaySec) external payable onlyOwner {
```
[BasePaymaster.sol#L75](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L75)

```solidity
contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol:
  336:  senderInfo.deposit = uint112(deposit - requiredPrefund);

  362:  paymasterInfo.deposit = uint112(deposit - requiredPreFund);
```
[EntryPoint.sol#L336](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L336), [EntryPoint.sol#L362](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L362)


### [G-16] Reduce the size of error messages (Long revert Strings)

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

13 results - 4 files:

```solidity
contracts/smart-contract-wallet/SmartAccount.sol:
   77:         require(msg.sender == owner, "Smart Account:: Sender is not authorized");

  110:         require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");

  128:         require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
```
[SmartAccount.sol#L77](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L77), [SmartAccount.sol#L110](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L110), [SmartAccount.sol#L128](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L128) 

```solidity
contracts/smart-contract-wallet/SmartAccountFactory.sol:
  18:         require(_baseImpl != address(0), "base wallet address can not be zero");
```
[SmartAccountFactory.sol#L18](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L18)

```solidity
contracts/smart-contract-wallet/libs/MultiSend.sol:
  27:         require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");
```
[MultiSend.sol#L27](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L27)


```solidity
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:
   36:         require(address(_entryPoint) != address(0), "VerifyingPaymaster: Entrypoint can not be zero address");

   37:         require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");

   49:         require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");

   50:         require(paymasterId != address(0), "Paymaster Id can not be zero address");

   66:         require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");

  107:         require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");

  108:         require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");

  109:         require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");
```
[VerifyingSingletonPaymaster.sol#L36](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L36), [VerifyingSingletonPaymaster.sol#L37](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L37), [VerifyingSingletonPaymaster.sol#L49](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L49), [VerifyingSingletonPaymaster.sol#L50](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L50), [VerifyingSingletonPaymaster.sol#L66](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L107), [VerifyingSingletonPaymaster.sol#L107](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L107), [VerifyingSingletonPaymaster.sol#L108](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L108), [VerifyingSingletonPaymaster.sol#L109](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L109)

**Recommendation:**
Revert strings > 32 bytes or use Custom Error()

### [G-17] Use ``double require`` instead of using ``&&``

Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

```solidity
3 results - 1 file

contracts\smart-contract-wallet\base\ModuleManager.sol:
34:    require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

49:    require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

68:    require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```
[ModuleManager.sol#L34](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34), [ModuleManager.sol#L49](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49), [ModuleManager.sol#L68](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68)

**Recommendation Code:**
 ```diff
contracts\smart-contract-wallet\base\ModuleManager.sol#L68
- 68:    require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
+          require(msg.sender != SENTINEL_MODULES, "BSA104");
+          require(modules[msg.sender] != address(0), "BSA104");
```

### [G-18] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

5 results - 2 files:
```solidity
contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol:
303:    if (mUserOp.paymaster != address(0) && mUserOp.paymaster.code.length == 0) {

321:    if (_deadline != 0 && _deadline < block.timestamp) {

365:    if (_deadline != 0 && _deadline < block.timestamp) {

410:    if (paymasterDeadline != 0 && paymasterDeadline < deadline) {
```
[EntryPoint.sol#L303](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L303), [EntryPoint.sol#L321](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L321), [EntryPoint.sol#L365](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L365), [EntryPoint.sol#L410](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L410)

```solidity
contracts\smart-contract-wallet\libs\Math.sol:
147:    if (rounding == Rounding.Up && mulmod(x, y, denominator) > 0) {
```
[Math.sol#L147](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L147)

**Recomendation Code:**
```diff
contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol#L410
- 410:    if (paymasterDeadline != 0 && paymasterDeadline < deadline) {
+         if (paymasterDeadline != 0) {
+           if (paymasterDeadline < deadline) {
+           }
+         } 
```

### [G-19]  Functions guaranteed to revert_ when callled by normal users can be marked `payable` 

If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

18 results - 5 files:
```solidity
contracts\smart-contract-wallet\SmartAccount.sol:
  109:     function setOwner(address _newOwner) external mixedAuth {

  120:     function updateImplementation(address _implementation) external mixedAuth {

  127:     function updateEntryPoint(address _newEntryPoint) external mixedAuth {

  449:     function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {

  455:     function pullTokens(address token, address dest, uint256 amount) external onlyOwner {

  460:     function execute(address dest, uint value, bytes calldata func) external onlyOwner{

  465:     function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{

  489:     function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {
       
  536:     function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
```
[SmartAccount.sol#L109](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109), [SmartAccount.sol#L120](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L120), [SmartAccount.sol#L127](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L127), [SmartAccount.sol#L449](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449), [SmartAccount.sol#L455](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455), [SmartAccount.sol#L460](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460), [SmartAccount.sol#L465](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465), [SmartAccount.sol#L489](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489), [SmartAccount.sol#L536](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L536)

```solidity
contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol:
   65:     function setSigner( address _newVerifyingSigner) external onlyOwner{
```
[VerifyingSingletonPaymaster.sol#L65](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65)

```solidity
contracts\smart-contract-wallet\base\FallbackManager.sol:
  26:     function setFallbackHandler(address handler) public authorized {
```
[FallbackManager.sol#L26](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L26)

```solidity
contracts\smart-contract-wallet\base\ModuleManager.sol:
  32:     function enableModule(address module) public authorized {

  47:     function disableModule(address prevModule, address module) public authorized {
```
[ModuleManager.sol#L32](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L32), [ModuleManager.sol#L47](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L47)


```solidity
contracts\smart-contract-wallet\paymasters\BasePaymaster.sol:
 24:     function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {

 67:     function withdrawTo(address payable withdrawAddress, uint256 amount) public virtual onlyOwner {

 75:     function addStake(uint32 unstakeDelaySec) external payable onlyOwner {

 90:     function unlockStake() external onlyOwner {

 99:     function withdrawStake(address payable withdrawAddress) external onlyOwner {
```
[BasePaymaster.sol#L24](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24), [BasePaymaster.sol#L67](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L67), [BasePaymaster.sol#L75](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L75), [BasePaymaster.sol#L90](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L90), [BasePaymaster.sol#L99](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L99) 


**Recommendation:**
Functions guaranteed to revert when called by normal users can be marked payable  (for only ```onlyOwner, mixedAuth, authorized``` functions)


### [G-20] Setting the _constructor_ to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

**Context:**
4 results - 4 files:
```solidity
contracts/smart-contract-wallet/Proxy.sol:
  15:     constructor(address _implementation) {
```
[Proxy.sol#L15](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L15)

```solidity
contracts/smart-contract-wallet/SmartAccountFactory.sol:
  17:     constructor(address _baseImpl) {
```
[SmartAccountFactory.sol#L17](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L17)

```solidity
contracts/smart-contract-wallet/libs/MultiSend.sol:
  12:     constructor() {
```
[MultiSend.sol#L12](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L12)

```solidity
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:
  35:     constructor(IEntryPoint _entryPoint, address _verifyingSigner) BasePaymaster(_entryPoint) {
```
[VerifyingSingletonPaymaster.sol#L35](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L35)

```solidity
contracts\smart-contract-wallet\paymasters\BasePaymaster.sol:
  20:     constructor(IEntryPoint _entryPoint) {
```
[BasePaymaster.sol#L20](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L20)

**Recommendation:**
Set the constructor to ```payable```


### [G-21] Use ``assembly`` to write _address storage values_ 

8 results - 4 files:
```solidity
contracts/smart-contract-wallet/SmartAccountFactory.sol:
  17     constructor(address _baseImpl) {
  19:         _defaultImpl = _baseImpl;
```
[SmartAccountFactory.sol#L19](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L19)

```solidity
contracts/smart-contract-wallet/SmartAccount.sol:
  109     function setOwner(address _newOwner) external mixedAuth {
  112:         owner = _newOwner;

  127     function updateEntryPoint(address _newEntryPoint) external mixedAuth {
  130:         _entryPoint = IEntryPoint(payable(_newEntryPoint));

  166      function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
  172:         owner = _owner;
  173:         _entryPoint =  IEntryPoint(payable(_entryPointAddress));

```
[SmartAccount.sol#L112](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L112), [SmartAccount.sol#L130](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L130), [SmartAccount.sol#L172-L173](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L172-L173)

```solidity
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:
  35     constructor(IEntryPoint _entryPoint, address _verifyingSigner) BasePaymaster(_entryPoint) {
  38:         verifyingSigner = _verifyingSigner;

  65     function setSigner( address _newVerifyingSigner) external onlyOwner{
  67:         verifyingSigner = _newVerifyingSigner;
```
[VerifyingSingletonPaymaster.sol#L38](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L38), [VerifyingSingletonPaymaster.sol#L67](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L67)

```solidity
contracts/smart-contract-wallet/paymasters/BasePaymaster.sol: 
  24     function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
  25:         entryPoint = _entryPoint;
  26     }
```
[BasePaymaster.sol#L25](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L25)

**Recommendation Code:**
```solidity
contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L25
    function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
        assembly {
            sstore(entryPoint.slot, _entryPoint)
        }
    }
```

### [G-22] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are.

6 results - 2 files:
```solidity
contracts\smart-contract-wallet\SmartAccount.sol:
  468:    for (uint i = 0; i < dest.length;) {
```
[SmartAccount.sol#L468](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L468)


```solidity
contracts\smart-contract-wallet\aa-4337\core\EntryPoint.sol:
  100:    for (uint256 i = 0; i < opasLen; i++) {

  107:    for (uint256 a = 0; a < opasLen; a++) {

  112:    for (uint256 i = 0; i < opslen; i++) {

  128:    for (uint256 a = 0; a < opasLen; a++) {

  134:    for (uint256 i = 0; i < opslen; i++) {
```
[EntryPoint.sol#L100](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100), [EntryPoint.sol#L107](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L107), [EntryPoint.sol#L112](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L112), [EntryPoint.sol#L128](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L128), [EntryPoint.sol#L134](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L134)

### [G-23] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses `OR/AND` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

8 results - 2 files:
```solidity
contracts/smart-contract-wallet/SmartAccount.sol:
   83:     require(msg.sender == owner || msg.sender == address(this),"Only owner or self");

  232:     require(success || _tx.targetTxGas != 0 || refundInfo.gasPrice != 0, "BSA013");

  495:     require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");

  511:     require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
````
[SmartAccount.sol#L83](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L83), [SmartAccount.sol#L232](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L232), [SmartAccount.sol#L495](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L495), [SmartAccount.sol#L511](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L511)


```solidity
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol:
  303:    if (mUserOp.paymaster != address(0) && mUserOp.paymaster.code.length == 0) {

  321:    if (_deadline != 0 && _deadline < block.timestamp) {

  365:    if (_deadline != 0 && _deadline < block.timestamp) {

  410:    if (paymasterDeadline != 0 && paymasterDeadline < deadline) {
```
[EntryPoint.sol#L303](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L303), [EntryPoint.sol#L321](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L321), [EntryPoint.sol#L365](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L365), [EntryPoint.sol#L410](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L410)

### [G-24] `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables

3 results - 1 file:
```solidity
contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol:
51:     paymasterIdBalances[paymasterId] += msg.value;

58:         paymasterIdBalances[msg.sender] -= amount;

128:     paymasterIdBalances[extractedPaymasterId] -= actualGasCost;

```
[VerifyingSingletonPaymaster.sol#L51](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L51), [VerifyingSingletonPaymaster.sol#L58](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L58), [VerifyingSingletonPaymaster.sol#L128](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L128)

```diff
contracts\smart-contract-wallet\paymasters\verifying\singleton\VerifyingSingletonPaymaster.sol#L128
- 128:     paymasterIdBalances[extractedPaymasterId] -= actualGasCost;
+ 128:     paymasterIdBalances[extractedPaymasterId] = paymasterIdBalances[extractedPaymasterId] - actualGasCost;

```

`x += y (x -= y)` costs more gas than `x = x  + y (x = x - y)` for state variables.


### [G-25] Use a more recent version of solidity

In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.

```solidity
36 results - 36 files:
contracts/smart-contract-wallet/BaseSmartAccount.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/Proxy.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/SmartAccount.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/SmartAccountFactory.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol:
  6: pragma solidity ^0.8.12;

contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol:
  2: pragma solidity ^0.8.12;

contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol:
  2: pragma solidity ^0.8.12;

contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol:
  2: pragma solidity ^0.8.12;

contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol:
  2: pragma solidity ^0.8.12;

contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol:
  6: pragma solidity ^0.8.12;

contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol:
  2: pragma solidity ^0.8.12;

contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol:
  2: pragma solidity ^0.8.12;

contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol:
  2: pragma solidity ^0.8.12;

contracts/smart-contract-wallet/aa-4337/utils/Exec.sol:
  2: pragma solidity >=0.7.5 <0.9.0;

contracts/smart-contract-wallet/base/Executor.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/base/FallbackManager.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/base/ModuleManager.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/common/Enum.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/common/SignatureDecoder.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/common/Singleton.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/interfaces/ERC721TokenReceiver.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/interfaces/ERC1155TokenReceiver.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/interfaces/IERC165.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/interfaces/ISignatureValidator.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/libs/LibAddress.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/libs/Math.sol:
  4: pragma solidity 0.8.12;

contracts/smart-contract-wallet/libs/MultiSend.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/paymasters/BasePaymaster.sol:
  2: pragma solidity ^0.8.12;

contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol:
  2: pragma solidity 0.8.12;

contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:
  2: pragma solidity 0.8.12;
```

### [G-26] Optimize names to save gas
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` SmartAccount.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

SmartAccount.sol  function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
affed0e0  =>  nonce()
ce03fdab  =>  nonce(uint256)
b0d691fe  =>  entryPoint()
13af4035  =>  setOwner(address)
025b22bc  =>  updateImplementation(address)
1b71bb6e  =>  updateEntryPoint(address)
f698da25  =>  domainSeparator()
3408e470  =>  getChainId()
3d46b819  =>  getNonce(uint256)
184b9559  =>  init(address,address,address)
6d5433e6  =>  max(uint256,uint256)
405c3941  =>  execTransaction(Transaction,uint256,FeeRefund,bytes)
1bb09224  =>  handlePayment(uint256,uint256,uint256,uint256,address,address)
a18f51e5  =>  handlePaymentRevert(uint256,uint256,uint256,uint256,address,address)
934f3a11  =>  checkSignatures(bytes32,bytes,bytes)
37cf6f29  =>  requiredTxGas(address,uint256,bytes,Enum.Operation)
c9f909f4  =>  getTransactionHash(address,uint256,bytes,Enum.Operation,uint256,uint256,uint256,uint256,address,address,uint256)
8d6a6751  =>  encodeTransactionData(Transaction,FeeRefund,uint256)
a9059cbb  =>  transfer(address,uint256)
ac85dca7  =>  pullTokens(address,address,uint256)
b61d27f6  =>  execute(address,uint256,bytes)
18dfb3c7  =>  executeBatch(address[],bytes[])
734cd1e2  =>  _call(address,uint256,bytes)
e8d655cf  =>  execFromEntryPoint(address,uint256,bytes,Enum.Operation,uint256)
be484bf7  =>  _requireFromEntryPointOrOwner()
ba74b602  =>  _validateAndUpdateNonce(UserOperation)
0f4cd016  =>  _validateSignature(UserOperation,bytes32,address)
c399ec88  =>  getDeposit()
4a58db19  =>  addDeposit()
4d44560d  =>  withdrawDepositTo(address,uint256)
01ffc9a7  =>  supportsInterface(bytes4)
```

### [G-27] Upgrade Solidity's optimizer

Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.

Set the optimization value higher than 800 in your hardhat.config.ts file.

```js
  30:   solidity: {
  31:     compilers: [
  32:       {
  33:         version: "0.8.12",
  34:         settings: {
  35:           optimizer: { enabled: true, runs: 200 },
  36:         },
  37:       },

```