## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| Prevent division by 0| 1 |
|[L-02]|  Use of EIP 4337, which is likely to change, not recommended for general use or application | 1 |
|[L-03]| Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when casting from uint256| 1 |
|[L-04]| Gas griefing/theft is possible on unsafe external call| 8 |
|[L-05]| Front running attacks by the `onlyOwner` | 1 |
|[L-06]| A single point of failure  | 14 |
|[L-07]| Loss of precision due to rounding  | 1 |
|[L-08]| No Storage Gap for ` BaseSmartAccount ` and `ModuleManager `| 2 |
|[L-09]| Missing Event for critical parameters init and change | 1 |
|[L-10]| Use `2StepSetOwner ` instead of ` setOwner `| 1 |
|[L-11]| init() function can be called by anybody| 1 |
|[L-12]| The minimum transaction value of 21,000 gas may change in the future| 1 |

Total 12 issues


### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01]|Insufficient coverage|1|
| [N-02] |Unused function parameter and local variable |2|
| [N-03] |Initial value check is missing in Set Functions| 3 |
| [N-04] |NatSpec comments should be increased in contracts |All Contracts|
| [N-05] |`Function writing` that does not comply with the `Solidity Style Guide` |All Contracts|
| [N-06] |Add a timelock to critical functions| 1|
| [N-07] |For modern and more readable code; update import usages| 116 |
| [N-08] |Include return parameters in NatSpec comments | All Contracts |
| [N-09] |Long lines are not suitable for the ‘Solidity Style Guide’| 9 |
| [N-10] |Need Fuzzing test| 23  |
| [N-11] |Test environment comments and codes should not be in the main version| 1 |
| [N-12] |Use of bytes.concat() instead of abi.encodePacked()| 5 |
| [N-13] |For functions, follow Solidity standard naming conventions (internal function style rule)| 13 |
| [N-14] |Omissions in Events| 1 |
| [N-15] |Open TODOs | 1 |
| [N-16] |Mark visibility of init(...) functions as ``external``| 1 |
| [N-17] |Use underscores for number literals| 2 |
| [N-18] |`Empty blocks` should be _removed_ or _Emit_ something| 2 |
| [N-19] |Use `require` instead of `assert`| 2 |
| [N-20] |Implement some type of version counter that will be incremented automatically for contract upgrades| 1 |
| [N-21] |Tokens accidentally sent to the contract cannot be recovered| 1 |
| [N-22] |Use a single file for all system-wide constants| 10 |
| [N-23] |Assembly Codes Specific – Should Have Comments | 72 |


Total 23 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Project Upgrade and Stop Scenario should be |
| [S-02] |Use descriptive names for Contracts and Libraries |

Total2 suggestions

### [L-01] Prevent division by 0

On several locations in the code precautions are not being taken for not dividing by 0, this will revert the code.
These functions can be calledd with 0 value in the input, this value is not checked for being bigger than 0, that means in some scenarios this can potentially trigger a division by zero.

[SmartAccount.sol#L247-L295](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L247-L295)


```solidity

2 results - 1 file

contracts/smart-contract-wallet/SmartAccount.sol:
  264:             payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);
  288:             payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);
```


```solidity
contracts/smart-contract-wallet/SmartAccount.sol:
  246  
  247:     function handlePayment(
  248:         uint256 gasUsed,
  249:         uint256 baseGas,
  250:         uint256 gasPrice,
  251:         uint256 tokenGasPriceFactor,
  252:         address gasToken,
  253:         address payable refundReceiver
  254:     ) private nonReentrant returns (uint256 payment) {
  255:         // uint256 startGas = gasleft();
  256:         // solhint-disable-next-line avoid-tx-origin
  257:         address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
  258:         if (gasToken == address(0)) {
  259:             // For ETH we will only adjust the gas price to not be higher than the actual used gas price
  260:             payment = (gasUsed + baseGas) * (gasPrice < tx.gasprice ? gasPrice : tx.gasprice);
  261:             (bool success,) = receiver.call{value: payment}("");
  262:             require(success, "BSA011");
  263:         } else {
  264:             payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);
  265:             require(transferToken(gasToken, receiver, payment), "BSA012");
  266:         }
  267:         // uint256 requiredGas = startGas - gasleft();
  268:         //console.log("hp %s", requiredGas);
  269:     }
  270: 
  271:     function handlePaymentRevert(
  272:         uint256 gasUsed,
  273:         uint256 baseGas,
  274:         uint256 gasPrice,
  275:         uint256 tokenGasPriceFactor,
  276:         address gasToken,
  277:         address payable refundReceiver
  278:     ) external returns (uint256 payment) {
  279:         uint256 startGas = gasleft();
  280:         // solhint-disable-next-line avoid-tx-origin
  281:         address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
  282:         if (gasToken == address(0)) {
  283:             // For ETH we will only adjust the gas price to not be higher than the actual used gas price
  284:             payment = (gasUsed + baseGas) * (gasPrice < tx.gasprice ? gasPrice : tx.gasprice);
  285:             (bool success,) = receiver.call{value: payment}("");
  286:             require(success, "BSA011");
  287:         } else {
  288:             payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);
  289:             require(transferToken(gasToken, receiver, payment), "BSA012");
  290:         }
  291:         uint256 requiredGas = startGas - gasleft();
  292:         //console.log("hpr %s", requiredGas);
  293:         // Convert response to string and return via error message
  294:         revert(string(abi.encodePacked(requiredGas)));
  295:     }

```

### [L-02] Use of EIP 4337, which is likely to change, not recommended for general use or application

```js
contracts/smart-contract-wallet/SmartAccountFactory.sol:
  28       * @param _owner EOA signatory of the wallet
  29:      * @param _entryPoint AA 4337 entry point address
  30       * @param _handler fallback handler address

  49       * @param _owner EOA signatory of the wallet
  50:      * @param _entryPoint AA 4337 entry point address
  51       * @param _handler fallback handler address

contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol:
  1  /**
  2:  ** Account-Abstraction (EIP-4337) singleton EntryPoint implementation.
  3   ** Only one instance required on each chain.

contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol:
  1  /**
  2:  ** Account-Abstraction (EIP-4337) singleton EntryPoint implementation.
  3   ** Only one instance required on each chain.

contracts/smart-contract-wallet/aa-4337/samples/DepositPaymaster.sol:
  21   * paymasterAndData holds the paymaster address followed by the token address to use.
  22:  * @notice This paymaster will be rejected by the standard rules of EIP4337, as it uses an external oracle.
  23   * (the standard rules ban accessing data of an external contract)
```

An account abstraction proposal which completely avoids the need for consensus-layer protocol changes.

However, this EIP has not been finalized yet, there is a warning situation that is not of general use.

If it is desired to be used, it is recommended to perform high-level security controls such as Formal Verification.

https://eips.ethereum.org/EIPS/eip-4337

### [L-03] Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when casting from uint256


```solidity

contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol:
  114       */
  115:     function withdrawTo(address payable withdrawAddress, uint256 withdrawAmount) external {
  116:         DepositInfo storage info = deposits[msg.sender];
  117:         require(withdrawAmount <= info.deposit, "Withdraw amount too large");
  118:         info.deposit = uint112(info.deposit - withdrawAmount);
  119:         emit Withdrawn(msg.sender, withdrawAddress, withdrawAmount);
  120:         (bool success,) = withdrawAddress.call{value : withdrawAmount}("");
  121:         require(success, "failed to withdraw");
  122:     }
  123  }

```
In the StakeManager contract, the `withdrawTo` function takes an argument `withdrawAmount` of type uint256

Now, in the function, the value of `withdrawAmount` is downcasted to uint112


Recommended Mitigation Steps:
Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when casting from uint256.


### [L-04] Gas griefing/theft is possible on unsafe external call

`return` data `(bool success,)` has to be stored due to EVM architecture, if in a usage like below, 'out' and 'outsize' values are given (0,0) . Thus, This storage disappears and may come from external contracts a possible Gas griefing/theft problem is avoided

https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19

There are 8 instances of the topic.

```diff
contracts\smart-contract-wallet\SmartAccount.sol#l451
  449     function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
- 451:       (bool success,) = dest.call{value:amount}("");                              
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


### [L-05] Front running attacks by the `onlyOwner` 


`verifyingSigner` value is not a constant value and can be changed with `setSigner` function, before a function using `verifyingSigner` state variable value in the project, `setSigner` function can be triggered by `onlyOwner` and operations can be blocked

```js
contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:
  64      */
  65:     function setSigner( address _newVerifyingSigner) external onlyOwner{
  66:         require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
  67:         verifyingSigner = _newVerifyingSigner;
  68:     }

```


### Recommended Mitigation Steps

Use a timelock to avoid instant changes of the parameters.

### [L-06]  A single point of failure 


## Impact

The `onlyOwner` role has a single point of failure and `onlyOwner` can use critical a few functions.


Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses.


` onlyOwner ` functions;
```js

14 results - 3 files

contracts/smart-contract-wallet/SmartAccount.sol:
   72:     // onlyOwner
   76:     modifier onlyOwner {
   81:     // onlyOwner OR self
  449:     function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
  455:     function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
  460:     function execute(address dest, uint value, bytes calldata func) external onlyOwner{
  465:     function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
  536:     function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {

contracts/smart-contract-wallet/paymasters/BasePaymaster.sol:
  24:     function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
  67:     function withdrawTo(address payable withdrawAddress, uint256 amount) public virtual onlyOwner {
  75:     function addStake(uint32 unstakeDelaySec) external payable onlyOwner {
  90:     function unlockStake() external onlyOwner {
  99:     function withdrawStake(address payable withdrawAddress) external onlyOwner {

contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:
  65:     function setSigner( address _newVerifyingSigner) external onlyOwner{


```

This increases the risk of ```A single point of failure```




## Tools Used
Manuel Code Review


## Recommended Mitigation Steps
Add a time lock to  critical functions. Admin-only functions that change critical parameters should emit events and have timelocks. 

Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.

Also detail them in documentation and NatSpec comments

### [L-07] Loss of precision due to rounding

Add scalars so roundings are negligible


```solidity

2 results - 1 file

contracts/smart-contract-wallet/SmartAccount.sol:
  264:             payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);
  288:             payment = (gasUsed + baseGas) * (gasPrice) / (tokenGasPriceFactor);
```



### [L-08] No Storage Gap for ` BaseSmartAccount ` and `ModuleManager `

[BaseSmartAccount.sol#L33](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L33)
[ModuleManager.sol#L9](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L9)

### Impact
For upgradeable contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

If no storage gap is added, when the upgradable contract introduces new variables, 
it may override the variables in the inheriting contract.

Storage gaps are a convention for reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts.
To create a storage gap, declare a fixed-size array in the base contract with an initial number of slots. 
This can be an array of uint256 so that each element reserves a 32 byte slot. Use the naming convention __gap so that OpenZeppelin Upgrades will recognize the gap:

Classification for a similar problem:
https://code4rena.com/reports/2022-05-alchemix/#m-05-no-storage-gap-for-upgradeable-contract-might-lead-to-storage-slot-collision

```js
contract Base {
    uint256 base1;
    uint256[49] __gap;
}

contract Child is Base {
    uint256 child;
}
```

Openzeppelin Storage Gaps notification:
```js
Storage Gaps
This makes the storage layouts incompatible, as explained in Writing Upgradeable Contracts. 
The size of the __gap array is calculated so that the amount of storage used by a contract 
always adds up to the same number (in this case 50 storage slots).
```



### Recommended Mitigation Steps
Consider adding a storage gap at the end of the upgradeable abstract contract
```js
uint256[50] private __gap;
```
### [L-09] Missing Event for critical parameters init and change

**Context:**
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

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit
### [L-10] Use `2StepSetOwner ` instead of ` setOwner `

```solidity
1 result - 1 file

contracts/smart-contract-wallet/SmartAccount.sol:
  109:     function setOwner(address _newOwner) external mixedAuth {
  110:         require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
  111:         address oldOwner = owner;
  112:         owner = _newOwner;
  113:         emit EOAChanged(address(this), oldOwner, _newOwner);
  114:     }

```

Use a 2 structure which is safer. 


### [L-11] init() function can be called by anybody

`init()` function can be called anybody when the contract is not initialized.

More importantly, if someone else runs this function, they will have full authority because of the `owner` state variable.

Here is a definition of `init()` function.

```solidity
contracts/smart-contract-wallet/SmartAccount.sol:
  166:     function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
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


### Recommended Mitigation Steps

Add a control that makes `init()` only call the Deployer Contract or EOA;

```js
if (msg.sender != DEPLOYER_ADDRESS) {
            revert NotDeployer();
        }
```


### [L-12] The minimum transaction value of 21,000 gas may change in the future

Any transaction has a 'base fee' of 21,000 gas in order to cover the cost of an elliptic curve operation that recovers the sender address from the signature, as well as the disk space of storing the transaction, according to the Ethereum White Paper


```solidity

contracts/smart-contract-wallet/SmartAccount.sol:
  192:     function execTransaction(
  193:         Transaction memory _tx,
  194:         uint256 batchId,
  195:         FeeRefund memory refundInfo,
  196:         bytes memory signatures
  197:     ) public payable virtual override returns (bool success) {
  198:         // initial gas = 21k + non_zero_bytes * 16 + zero_bytes * 4
  199:         //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
  200:         uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
  201:         //console.log("init %s", 21000 + msg.data.length * 8);
  202:         bytes32 txHash;
  203:         // Use scope here to limit variable lifetime and prevent `stack too deep` errors
  204:         {
  205:             bytes memory txHashData =
  206:                 encodeTransactionData(
  207:                     // Transaction info
  208:                     _tx,
  209:                     // Payment info
  210:                     refundInfo,
  211:                     // Signature info
  212:                     nonces[batchId]
  213:                 );
  214:             // Increase nonce and execute transaction.
  215:             // Default space aka batchId is 0
  216:             nonces[batchId]++;
  217:             txHash = keccak256(txHashData);
  218:             checkSignatures(txHash, txHashData, signatures);
  219:         }


```

https://ethereum-magicians.org/t/some-medium-term-dust-cleanup-ideas/6287

Why do txs cost 21000 gas?
To understand how special-purpose cheap withdrawals could be done, it helps first to understand what goes into the 21000 gas in a tx. The cost of processing a tx includes:
Two account writes (a balance-editing CALL normally costs 9000 gas)
A signature verification (compare: the ECDSA precompile costs 3000 gas)
The transaction data (~100 bytes, so 1600 gas, though originally it cost 6800)
Some more gas was tacked on to account for transaction-specific overhead, bringing the total to 21000.


[protocol_params.go#L31](https://github.com/ethereum/go-ethereum/blob/b8f9b3779fbdc62d5a935b57f1360608fa4600b4/params/protocol_params.go#L31)

The minimum transaction value of 21,000 gas may change in the future, so it is recommended to make this value updatable.



### Recommended Mitigation Steps

Add this code;

```js

uint256 txcost = 21_000;

 function changeTxCost(uint256 _amount) public onlyOwner {
        txcost = _amount;
    }

```




### [N-01] Insufficient coverage

**Description:**
The test coverage rate of the project is 76%. Testing all functions is best practice in terms of security criteria.

```js
-------------------------------------------------------|----------|----------|----------|----------|----------------|
File                                                   |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
-------------------------------------------------------|----------|----------|----------|----------|----------------|
 smart-contract-wallet/                                |     35.1 |       16 |    29.87 |    35.79 |                |
  BaseSmartAccount.sol                                 |        0 |        0 |        0 |        0 |... 107,108,109 |
  Proxy.sol                                            |      100 |      100 |      100 |      100 |                |
  SmartAccount.sol                                     |    63.92 |     32.2 |    52.94 |    64.23 |... 528,537,546 |
  SmartAccountFactory.sol                              |    81.82 |       50 |       75 |    76.47 |    54,56,59,60 |
 smart-contract-wallet/aa-4337/core/                   |    98.46 |    81.15 |    93.18 |    97.16 |                |
  EntryPoint.sol                                       |      100 |    88.16 |      100 |    98.09 |373,421,463,471 |
  SenderCreator.sol                                    |      100 |      100 |      100 |      100 |                |
  StakeManager.sol                                     |      100 |    76.92 |      100 |      100 |                |
 smart-contract-wallet/aa-4337/interfaces/             |       80 |       25 |       80 |    84.62 |                |
  UserOperation.sol                                    |       80 |       25 |       80 |    84.62 |          53,78 |
 smart-contract-wallet/base/                           |       24 |    17.86 |    27.27 |    23.08 |                |
  Executor.sol                                         |       75 |       75 |      100 |      100 |                |
  FallbackManager.sol                                  |       25 |        0 |    33.33 |    33.33 |    27,28,33,35 |
  ModuleManager.sol                                    |    11.76 |     9.09 |    14.29 |    10.34 |... 124,126,129 |
 smart-contract-wallet/common/                         |       25 |        0 |    33.33 |    33.33 |                |
  Enum.sol                                             |      100 |      100 |      100 |      100 |                |
  SecuredTokenTransfer.sol                             |      100 |      100 |      100 |      100 |                |
  SignatureDecoder.sol                                 |      100 |      100 |      100 |      100 |                |
  Singleton.sol                                        |        0 |      100 |        0 |        0 |       13,15,22 |
 smart-contract-wallet/interfaces/                     |      100 |      100 |      100 |      100 |                |
  ERC1155TokenReceiver.sol                             |      100 |      100 |      100 |      100 |                |
  ERC721TokenReceiver.sol                              |      100 |      100 |      100 |      100 |                |
  ERC777TokensRecipient.sol                            |      100 |      100 |      100 |      100 |                |
  IERC1271Wallet.sol                                   |      100 |      100 |      100 |      100 |                |
  IERC165.sol                                          |      100 |      100 |      100 |      100 |                |
  ISignatureValidator.sol                              |      100 |      100 |      100 |      100 |                |
 smart-contract-wallet/libs/                           |     1.45 |     1.47 |    13.64 |      2.7 |                |
  LibAddress.sol                                       |        0 |      100 |        0 |        0 |       12,14,15 |
  Math.sol                                             |        0 |        0 |        0 |        0 |... 340,341,342 |
  MultiSend.sol                                        |      100 |       50 |      100 |      100 |                |
  MultiSendCallOnly.sol                                |      100 |      100 |      100 |      100 |                |
 smart-contract-wallet/paymasters/                     |    23.53 |     8.33 |    21.43 |    26.32 |                |
  BasePaymaster.sol                                    |     9.09 |     8.33 |    18.18 |    15.38 |... ,91,100,105 |
  PaymasterHelpers.sol                                 |       50 |      100 |    33.33 |       50 |       28,44,45 |
 smart-contract-wallet/paymasters/verifying/singleton/ |       45 |    27.27 |     37.5 |    40.74 |                |
  VerifyingSingletonPaymaster.sol                      |       45 |    27.27 |     37.5 |    40.74 |... 126,127,128 |
-------------------------------------------------------|----------|----------|----------|----------|----------------|

```
Due to its capacity, test coverage is expected to be 100%.


### [N-02] Unused function parameter and local variable



```js

Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
  --> contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol:25:9:
   |
25 |         UserOperation calldata op,


Warning: Unused local variable.
  --> contracts/smart-contract-wallet/utils/GasEstimatorSmartAccount.sol:20:5:
   |
20 |     address _wallet = SmartAccountFactory(_factory).deployCounterFactualWallet(_owner, _entryPoint, _handler, _index);


```
### [N-03] Initial value check is missing in Set Functions

**Context:**


```js
3 results - 3 files

contracts/smart-contract-wallet/SmartAccount.sol:
  109:     function setOwner(address _newOwner) external mixedAuth {

contracts/smart-contract-wallet/base/ModuleManager.sol:
  20:     function setupModules(address to, bytes memory data) internal {

contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:
  65:     function setSigner( address _newVerifyingSigner) external onlyOwner{


```

Checking whether the current value and the new value are the same should be added



### [N-04] NatSpec comments should be increased in contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts

### [N-05] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-06] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).
Consider adding a timelock to:

```solidity

contracts/smart-contract-wallet/SmartAccount.sol:
  108  
  109:     function setOwner(address _newOwner) external mixedAuth {
  110:         require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
  111:         address oldOwner = owner;
  112:         owner = _newOwner;
  113:         emit EOAChanged(address(this), oldOwner, _newOwner);
  114:     }
  115 
```


### [N-07] For modern and more readable code; update import usages

**Context:**
All Contracts (116 results - 40 files)

**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```

### [N-08] Include return parameters in NatSpec comments

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
Include return parameters in NatSpec comments

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

## [N-09] Long lines are not suitable for the ‘Solidity Style Guide’

**Context:**
[EntryPoint.sol#L168](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L168)
[EntryPoint.sol#L289](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L289)
[EntryPoint.sol#L319](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L319)
[EntryPoint.sol#L349](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L349)
[EntryPoint.sol#L363](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L363)
[EntryPoint.sol#L409](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L409)
[EntryPoint.sol#L440](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L440)
[SmartAccount.sol#L239](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L239)
[SmartAccount.sol#L489](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489)


**Description:**
It is generally recommended that lines in the source code should not exceed 80-120 characters. Today's screens are much larger, so in some cases it makes sense to expand that. The lines above should be split when they reach that length, as the files will most likely be on GitHub and GitHub always uses a scrollbar when the length is more than 164 characters.

(why-is-80-characters-the-standard-limit-for-code-width)[https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width]

**Recommendation:**
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction

```js
thisFunctionCallIsReallyLong(
    longArgument1,
    longArgument2,
    longArgument3
);
```

### [N-10] Need Fuzzing test

**Context:**

```solidity
23 results - 5 files

contracts/smart-contract-wallet/SmartAccount.sol:
  470:             unchecked {

contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol:
   73:     unchecked {
   85:     } //unchecked
  185:     unchecked {
  249:     unchecked {
  291:     unchecked {
  350:     unchecked {
  417:     unchecked {
  442:     unchecked {
  477:     } // unchecked
  485:     unchecked {

contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol:
  46:     unchecked {

contracts/smart-contract-wallet/libs/Math.sol:
   60:         unchecked {
  179:         unchecked {
  195:         unchecked {
  207:         unchecked {
  248:         unchecked {
  260:         unchecked {
  297:         unchecked {
  311:         unchecked {
  340:         unchecked {

contracts/smart-contract-wallet/libs/Strings.sol:
  19:         unchecked {
  44:         unchecked {


```

**Description:**
In total 5 contracts, 23 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

**Recommendation:**
Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:
_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

### [N-11] Test environment comments and codes should not be in the main version


```solidity

1 result - 1 file

contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol:
  10: // import "../samples/Signatures.sol";

```

### [N-12] Use of bytes.concat() instead of abi.encodePacked()

```solidity
5 results - 3 files

contracts/smart-contract-wallet/SmartAccount.sol:
  445:         return abi.encodePacked(bytes1(0x19), bytes1(0x01), domainSeparator(), safeTxHash);

contracts/smart-contract-wallet/SmartAccountFactory.sol:
  35:         bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
  54:         bytes memory deploymentData = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));
  69:        bytes memory code = abi.encodePacked(type(Proxy).creationCode, uint(uint160(_defaultImpl)));

contracts/smart-contract-wallet/SmartAccountNoAuth.sol:
  435:         return abi.encodePacked(bytes1(0x19), bytes1(0x01), domainSeparator(), safeTxHash);


```
Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, bytes.concat() is enabled

Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,)

### [N-13] For functions, follow Solidity standard naming conventions (internal function style rule)

**Context:**
```solidity
38 results - 11 files

contracts/smart-contract-wallet/SmartAccount.sol:
  181:     function max(uint256 a, uint256 b) internal pure returns (uint256) {

contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol:
  23:     function getStakeInfo(address addr) internal view returns (StakeInfo memory info) {
  38:     function internalIncrementDeposit(address account, uint256 amount) internal {

contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol:
  36:     function getSender(UserOperation calldata userOp) internal pure returns (address) {
  45:     function gasPrice(UserOperation calldata userOp) internal view returns (uint256) {
  57:     function pack(UserOperation calldata userOp) internal pure returns (bytes memory ret) {
  73:     function hash(UserOperation calldata userOp) internal pure returns (bytes32) {
  77:     function min(uint256 a, uint256 b) internal pure returns (uint256) {

contracts/smart-contract-wallet/aa-4337/utils/Exec.sol:
  13:     ) internal returns (bool success) {
  23:     ) internal view returns (bool success) {
  33:     ) internal returns (bool success) {
  40:     function getReturnData() internal pure returns (bytes memory returnData) {
  51:     function revertWithData(bytes memory returnData) internal pure {
  57:     function callAndRevert(address to, bytes memory data) internal {

contracts/smart-contract-wallet/base/Executor.sol:
  19:     ) internal returns (bool success) {

contracts/smart-contract-wallet/base/FallbackManager.sol:
  14:     function internalSetFallbackHandler(address handler) internal {

contracts/smart-contract-wallet/base/ModuleManager.sol:
  16:     address internal constant SENTINEL_MODULES = address(0x1);
  18:     mapping(address => address) internal modules;
  20:     function setupModules(address to, bytes memory data) internal {

contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol:
  14:     ) internal returns (bool transferred) {

contracts/smart-contract-wallet/libs/LibAddress.sol:
  11:   function isContract(address account) internal view returns (bool) {

contracts/smart-contract-wallet/libs/Math.sol:
   19:     function max(uint256 a, uint256 b) internal pure returns (uint256) {
   26:     function min(uint256 a, uint256 b) internal pure returns (uint256) {
   34:     function average(uint256 a, uint256 b) internal pure returns (uint256) {
   45:     function ceilDiv(uint256 a, uint256 b) internal pure returns (uint256) {
   59:     ) internal pure returns (uint256 result) {
  145:     ) internal pure returns (uint256) {
  158:     function sqrt(uint256 a) internal pure returns (uint256) {
  194:     function sqrt(uint256 a, Rounding rounding) internal pure returns (uint256) {
  205:     function log2(uint256 value) internal pure returns (uint256) {
  247:     function log2(uint256 value, Rounding rounding) internal pure returns (uint256) {
  258:     function log10(uint256 value) internal pure returns (uint256) {
  296:     function log10(uint256 value, Rounding rounding) internal pure returns (uint256) {
  309:     function log256(uint256 value) internal pure returns (uint256) {
  339:     function log256(uint256 value, Rounding rounding) internal pure returns (uint256) {

contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol:
  27:     ) internal pure returns (bytes memory context) {
  34:     function decodePaymasterData(UserOperation calldata op) internal pure returns (PaymasterData memory) {
  43:     function decodePaymasterContext(bytes memory context) internal pure returns (PaymasterContext memory) {


```


**Description:**
The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)


### [N-14] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

```solidity

contracts/smart-contract-wallet/paymasters/BasePaymaster.sol:
  23: 
  24:     function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
  25:         entryPoint = _entryPoint;
  26:     }

```
### [N-15] Open TODOs

**Context:**
```solidity
contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol:
  255:         // TODO: copy logic of gasPrice?

```

**Recommendation:**
Use temporary TODOs as you work on a feature, but make sure to treat them before merging. Either add a link to a proper issue in your TODO, or remove it from the code.


### [N-16] Mark visibility of init(...) functions as ``external``

```solidity
contracts/smart-contract-wallet/SmartAccount.sol:
  166:     function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
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

**Description:**

External instead of public would give more the sense of the init(...) functions to behave like a constructor (only called on deployment, so should only be called externally)

Security point of view, it might be safer so that it cannot be called internally by accident in the child contract 

It might cost a bit less gas to use external over public 


It is possible to override a function from external to public (= "opening it up") ✅
but it is not possible to override a function from public to external (= "narrow it down"). ❌

For above reasons you can change init(...) to external


https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750
### [N-17] Use underscores for number literals

```diff
contracts/smart-contract-wallet/SmartAccount.sol:
-  200:         uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
+	        uint256 startGas = gasleft() + 21_000 + msg.data.length * 8;

contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol:
- 22:             let success := call(sub(gas(), 10000), token, 0, add(data, 0x20), mload(data), 0, 0x20)
+ 	          let success := call(sub(gas(), 10_000), token, 0, add(data, 0x20), mload(data), 0, 0x20)

```
**Description:**
 There is   occasions where certain number have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

**Recommendation:**
Consider using underscores for number literals to improve its readability.

### [N-18] `Empty blocks` should be _removed_ or _Emit_ something

**Description:**
Code contains empty block

```solidity

2 results - 2 files

contracts/smart-contract-wallet/SmartAccount.sol:
  550:     receive() external payable {}

contracts/smart-contract-wallet/aa-4337/samples/SimpleAccount.sol:
  41:     receive() external payable {}
```

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
### [N-19] Use `require` instead of `assert`

```solidity
2 results - 2 files

contracts/smart-contract-wallet/Proxy.sol:
  15      constructor(address _implementation) {
  16:          assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));

contracts/smart-contract-wallet/common/Singleton.sol:
  12      function _setImplementation(address _imp) internal {
  13:         assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));


```

**Description:**
Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.

assert() and ruqire();
The big difference between the two is that the `assert()`function when false, uses up all the remaining gas and reverts all the changes made.
Meanwhile, a  `require()` function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.
This is the most common Solidity function used by developers for debugging and error handling.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type Panic(uint256).Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".

### [N-20] Implement some type of version counter that will be incremented automatically for contract upgrades

As part of the upgradeability of  Proxies , the contract can be upgraded multiple times, where it is a systematic approach to record the version of each upgrade.

```js
contracts/smart-contract-wallet/SmartAccount.sol:
  120:     function updateImplementation(address _implementation) external mixedAuth {
  121:         require(_implementation.isContract(), "INVALID_IMPLEMENTATION");
  122:         _setImplementation(_implementation);
  123:         // EOA + Version tracking
  124:         emit ImplementationUpdated(address(this), VERSION, _implementation);
  125:     }


```
I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

**Recommendation:**
```js

uint256 public VERSION;

contracts/smart-contract-wallet/SmartAccount.sol:
  120:     function updateImplementation(address _implementation) external mixedAuth {
  121:         require(_implementation.isContract(), "INVALID_IMPLEMENTATION");
  122:         _setImplementation(_implementation);
  123:         // EOA + Version tracking
  124:         emit ImplementationUpdated(address(this), ++VERSION, _implementation);
  125:     }

```
### [N-21] Tokens accidentally sent to the contract cannot be recovered


It can't be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

### Recommended Mitigation Steps

Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```

### [N-22] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

```solidity
10 results - 7 files

contracts/smart-contract-wallet/Proxy.sol:
  11:     bytes32 internal constant _IMPLEMENTATION_SLOT = 0x37722d148fb373b961a84120b6c8d209709b45377878a466db32bbc40d95af26;

contracts/smart-contract-wallet/SmartAccount.sol:
  25:      ISignatureValidatorConstants,
  36:     string public constant VERSION = "1.0.2"; // using AA 0.3.0
  42:     bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH = 0x47e79534a245952e8b16893a336b85a3d9ea9fa8c573f3d803afb92a79469218;
  48:     bytes32 internal constant ACCOUNT_TX_TYPEHASH = 0xc2595443c361a1f264c73470b9410fd67ac953ebd1a3ae63a2f514f3f014cf07;

contracts/smart-contract-wallet/SmartAccountFactory.sol:
  11:     string public constant VERSION = "1.0.2";

contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol:
  28:     address private constant SIMULATE_FIND_AGGREGATOR = address(1);

contracts/smart-contract-wallet/base/FallbackManager.sol:
  12:     bytes32 internal constant FALLBACK_HANDLER_STORAGE_SLOT = 0x6c9a6c4a39284e37ed1cf53d337577d14212a4870fb976a4366c693b939918d5;

contracts/smart-contract-wallet/base/ModuleManager.sol:
  16:     address internal constant SENTINEL_MODULES = address(0x1);

contracts/smart-contract-wallet/common/Singleton.sol:
  10:     bytes32 internal constant _IMPLEMENTATION_SLOT = 0x37722d148fb373b961a84120b6c8d209709b45377878a466db32bbc40d95af26;

```


### [N-23] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does


This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.


```solidity
72 results - 22 files

contracts/smart-contract-wallet/BaseSmartAccount.sol:
  5: /* solhint-disable no-inline-assembly */

contracts/smart-contract-wallet/Proxy.sol:
  17:          assembly {
  24:         // solhint-disable-next-line no-inline-assembly
  25:         assembly {

contracts/smart-contract-wallet/SmartAccount.sol:
  142:         // solhint-disable-next-line no-inline-assembly
  143:         assembly {
  329:                 // solhint-disable-next-line no-inline-assembly
  330:                 assembly {
  337:                 // solhint-disable-next-line no-inline-assembly
  338:                 assembly {
  480:             assembly {

contracts/smart-contract-wallet/SmartAccountFactory.sol:
  36:         // solhint-disable-next-line no-inline-assembly
  37:         assembly {
  55:         // solhint-disable-next-line no-inline-assembly
  56:         assembly {

contracts/smart-contract-wallet/SmartAccountNoAuth.sol:
  142:         // solhint-disable-next-line no-inline-assembly
  143:         assembly {
  324:                 // solhint-disable-next-line no-inline-assembly
  325:                 assembly {
  332:                 // solhint-disable-next-line no-inline-assembly
  333:                 assembly {
  470:             assembly {

contracts/smart-contract-wallet/aa-4337/core/BaseAccount.sol:
  5: /* solhint-disable no-inline-assembly */

contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol:
    9: /* solhint-disable no-inline-assembly */
  501:         assembly {offset := data}
  505:         assembly {data := offset}
  512:         assembly {mstore(0, number())}

contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol:
  19:         /* solhint-disable no-inline-assembly */
  20:         assembly {

contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol:
  9: /* solhint-disable no-inline-assembly */

contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol:
   4: /* solhint-disable no-inline-assembly */
  39:         assembly {data := calldataload(userOp)}
  63:         assembly {

contracts/smart-contract-wallet/aa-4337/utils/Exec.sol:
   4: // solhint-disable no-inline-assembly
  14:         assembly {
  24:         assembly {
  34:         assembly {
  41:         assembly {
  52:         assembly {

contracts/smart-contract-wallet/base/Executor.sol:
  21:             // solhint-disable-next-line no-inline-assembly
  22:             assembly {
  26:             // solhint-disable-next-line no-inline-assembly
  27:             assembly {

contracts/smart-contract-wallet/base/FallbackManager.sol:
  16:         // solhint-disable-next-line no-inline-assembly
  17:         assembly {
  34:         // solhint-disable-next-line no-inline-assembly
  35:         assembly {

contracts/smart-contract-wallet/base/ModuleManager.sol:
   87:         // solhint-disable-next-line no-inline-assembly
   88:         assembly {
  128:         // solhint-disable-next-line no-inline-assembly
  129:         assembly {

contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol:
  18:         // solhint-disable-next-line no-inline-assembly
  19:         assembly {

contracts/smart-contract-wallet/common/SignatureDecoder.sol:
  22:         // solhint-disable-next-line no-inline-assembly
  23:         assembly {

contracts/smart-contract-wallet/common/Singleton.sol:
  14:         // solhint-disable-next-line no-inline-assembly
  15:         assembly {
  21:         // solhint-disable-next-line no-inline-assembly
  22:         assembly {

contracts/smart-contract-wallet/libs/LibAddress.sol:
  13:     // solhint-disable-next-line no-inline-assembly
  14:     assembly { csize := extcodesize(account) }

contracts/smart-contract-wallet/libs/Math.sol:
   66:             assembly {
   86:             assembly {
  100:             assembly {

contracts/smart-contract-wallet/libs/MultiSend.sol:
  28:         // solhint-disable-next-line no-inline-assembly
  29:         assembly {

contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol:
  22:         // solhint-disable-next-line no-inline-assembly
  23:         assembly {

contracts/smart-contract-wallet/libs/Strings.sol:
  23:             /// @solidity memory-safe-assembly
  24:             assembly {
  29:                 /// @solidity memory-safe-assembly
  30:                 assembly {

```

### [S-01] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

### [S-02] Use descriptive names for Contracts and Libraries


This codebase will be difficult to navigate, as there are no descriptive naming conventions that specify which files should contain meaningful logic.

Prefixes should be added like this by filing:

Interface I_
absctract contracts Abs_
Libraries Lib_

We recommend that you implement this or a similar agreement.
