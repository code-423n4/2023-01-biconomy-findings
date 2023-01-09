## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Avoid using `tx.origin` | 3 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Use of `ecrecover` is susceptible to signature malleability | 2 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Init functions are susceptible to front-running | 1 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Low Level Calls With Solidity Version 0.8.14 Can Result In Optimiser Bug | 6 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Missing parameter validation in `constructor` | 1 |
| [LOW&#x2011;6](#LOW&#x2011;6) | Missing Contract-existence Checks Before Low-level Calls | 10 |
| [LOW&#x2011;7](#LOW&#x2011;7) | Contracts are not using their OZ Upgradeable counterparts | 9 |
| [LOW&#x2011;8](#LOW&#x2011;8) | Remove unused code | 5 |
| [LOW&#x2011;9](#LOW&#x2011;9) | `require()` should be used instead of `assert()` | 1 |
| [LOW&#x2011;10](#LOW&#x2011;10) | Unused `receive()` Function Will Lock Ether In Contract  | 1 |
| [LOW&#x2011;11](#LOW&#x2011;11) | Missing Non Reentrancy Modifiers | 6

Total: 45 contexts over 11 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Critical Changes Should Use Two-step Procedure | 6 |
| [NC&#x2011;2](#NC&#x2011;2) | Function creates dirty bits | 7 |
| [NC&#x2011;3](#NC&#x2011;3) | Function writing that does not comply with the Solidity Style Guide  | 37 |
| [NC&#x2011;4](#NC&#x2011;4) | Use `delete` to Clear Variables | 4 |
| [NC&#x2011;5](#NC&#x2011;5) | No need to initialize uints to zero | 11 |
| [NC&#x2011;6](#NC&#x2011;6) | Lines are too long | 3 |
| [NC&#x2011;7](#NC&#x2011;7) | Missing event for critical parameter change | 4 |
| [NC&#x2011;8](#NC&#x2011;8) | Implementation contract may not be initialized | 6 |
| [NC&#x2011;9](#NC&#x2011;9) | Non-usage of specific imports | 43 |
| [NC&#x2011;10](#NC&#x2011;10) | Use a more recent version of Solidity | 37 |
| [NC&#x2011;11](#NC&#x2011;11) | Open TODOs | 1 |
| [NC&#x2011;12](#NC&#x2011;12) | Use `bytes.concat()` | 16 |
| [NC&#x2011;13](#NC&#x2011;13) | Use of Block.Timestamp | 5 |
| [NC&#x2011;14](#NC&#x2011;14) | Commented Code | All in-scope contracts
| [NC&#x2011;15](#NC&#x2011;15) | Missing/In-complete NATSPEC | All in-scope contracts

Total: 252 contexts over 15 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Avoid using `tx.origin`

`tx.origin` is a global variable in Solidity that returns the address of the account that sent the transaction.

Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.

This can make it easier to create a vault on behalf of another user with an external administrator (by receiving it as an argument).

#### <ins>Proof Of Concept</ins>

```solidity
257: address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L257

```solidity
281: address payable receiver = refundReceiver == address(0) ? payable(tx.origin) : refundReceiver;
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L281

```solidity
511: require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L511






### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Use of `ecrecover` is susceptible to signature malleability

The built-in EVM precompile `ecrecover` is susceptible to signature malleability, which could lead to replay attacks.
References:  https://swcregistry.io/docs/SWC-117,  https://swcregistry.io/docs/SWC-121, and  https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57.
While this is not immediately exploitable, this may become a vulnerability if used elsewhere.

#### <ins>Proof Of Concept</ins>


```solidity
347: function checkSignatures(
        bytes32 dataHash,
        bytes memory data,
        bytes memory signatures
    ) public view virtual {
        uint8 v;
        bytes32 r;
        bytes32 s;
        uint256 i = 0;
        address _signer;
        (v, r, s) = signatureSplit(signatures, i);
        
        if(v == 0) {
            
            
            _signer = address(uint160(uint256(r)));

            
                
                
                require(uint256(s) >= uint256(1) * 65, "BSA021");

                
                require(uint256(s) + 32 <= signatures.length, "BSA022");

                
                uint256 contractSignatureLen;
                
                assembly {
                    contractSignatureLen := mload(add(add(signatures, s), 0x20))
                }
                require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");

                
                bytes memory contractSignature;
                
                assembly {
                    
                    contractSignature := add(add(signatures, s), 0x20)
                }
                require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");
        }
        else if(v > 30) {
            
            
            _signer = ecrecover(keccak256(abi.encodePacked("/x19Ethereum Signed Message:/n32", dataHash)), v - 4, r, s);
            require(_signer == owner, "INVALID_SIGNATURE");
        } else {
            _signer = ecrecover(dataHash, v, r, s);
            require(_signer == owner, "INVALID_SIGNATURE");
        }
    }
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L302-L353

```solidity
350: function checkSignatures(
        bytes32 dataHash,
        bytes memory data,
        bytes memory signatures
    ) public view virtual {
        uint8 v;
        bytes32 r;
        bytes32 s;
        uint256 i = 0;
        address _signer;
        (v, r, s) = signatureSplit(signatures, i);
        
        if(v == 0) {
            
            
            _signer = address(uint160(uint256(r)));

            
                
                
                require(uint256(s) >= uint256(1) * 65, "BSA021");

                
                require(uint256(s) + 32 <= signatures.length, "BSA022");

                
                uint256 contractSignatureLen;
                
                assembly {
                    contractSignatureLen := mload(add(add(signatures, s), 0x20))
                }
                require(uint256(s) + 32 + contractSignatureLen <= signatures.length, "BSA023");

                
                bytes memory contractSignature;
                
                assembly {
                    
                    contractSignature := add(add(signatures, s), 0x20)
                }
                require(ISignatureValidator(_signer).isValidSignature(data, contractSignature) == EIP1271_MAGIC_VALUE, "BSA024");
        }
        else if(v > 30) {
            
            
            _signer = ecrecover(keccak256(abi.encodePacked("/x19Ethereum Signed Message:/n32", dataHash)), v - 4, r, s);
            require(_signer == owner, "INVALID_SIGNATURE");
        } else {
            _signer = ecrecover(dataHash, v, r, s);
            require(_signer == owner, "INVALID_SIGNATURE");
        }
    }
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L350



#### Recommended Mitigation Steps
Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol#L138-L149


### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Init function is susceptible to front-running

Most contracts use an init pattern (instead of a constructor) to initialize contract parameters. Unless these are enforced to be atomic with contact deployment via deployment script or factory contracts, they are susceptible to front-running race conditions where an attacker/griefer can front-run (cannot access control because admin roles are not initialized) to initially with their own (malicious) parameters upon detecting (if an event is emitted) which the contract deployer has to redeploy wasting gas and risking other transactions from interacting with the attacker-initialized contract.

Many init functions do not have an explicit event emission which makes monitoring such scenarios harder. All of them have re-init checks; while many are explicit some (those in auction contracts) have implicit reinit checks in initAccessControls() which is better if converted to an explicit check in the main init function itself.
(details credit to: code-423n4/2021-09-sushimiso-findings#64)

#### <ins>Proof Of Concept</ins>


```solidity
166: function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166



#### <ins>Recommended Mitigation Steps</ins>

Ensure atomic calls to init functions along with deployment via robust deployment scripts or factory contracts. Emit explicit events for initializations of contracts. Enforce prevention of re-initializations via explicit setting/checking of boolean initialized variables in the main init function instead of downstream function checks.



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Low Level Calls With Solidity Version Before 0.8.14 Can Result In Optimiser Bug

The project contracts in scope are using low level calls with solidity version before 0.8.14 which can result in optimizer bug.
https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

Simliar findings in Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#5-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug

#### <ins>Proof Of Concept</ins>

POC can be found in the above medium reference url.

Functions that execute low level calls in contracts with solidity version under 0.8.14

```solidity
512: assembly {mstore(0, number())}
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L512

```solidity
63: assembly {
        let ofs := userOp
        let len := sub(sub(sig.offset, ofs), 32)
        ret := mload(0x40)
        mstore(0x40, add(ret, add(len, 32)))
        mstore(ret, len)
        calldatacopy(add(ret, 32), ofs, len)
    }
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L63-L70

```solidity
41: assembly {
        let ptr := mload(0x40)
        mstore(0x40, add(ptr, add(returndatasize(), 0x20)))
        mstore(ptr, returndatasize())
        returndatacopy(add(ptr, 0x20), 0, returndatasize())
        returnData := ptr
    }
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L41-L47

```solidity
35: assembly {
        let handler := sload(slot)
        if iszero(handler) {
            return(0, 0)
        }
        calldatacopy(0, 0, calldatasize())
        // The msg.sender address is shifted to the left by 12 bytes to remove the padding
        // Then the address without padding is stored right after the calldata
        mstore(calldatasize(), shl(96, caller()))
        // Add 20 bytes for the address appended add the end
        let success := call(gas(), handler, 0, 0, add(calldatasize(), 20), 0, 0)
        returndatacopy(0, 0, returndatasize())
        if iszero(success) {
            revert(0, returndatasize())
        }
        return(0, returndatasize())
    }
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L35-L51

```solidity
88: assembly {
        // Load free memory location
        let ptr := mload(0x40)
        // We allocate memory for the return data by setting the free memory location to
        // current free memory location + data size + 32 bytes for data size value
        mstore(0x40, add(ptr, add(returndatasize(), 0x20)))
        // Store the size
        mstore(ptr, returndatasize())
        // Store the data
        returndatacopy(add(ptr, 0x20), 0, returndatasize())
        // Point the return data to the correct memory location
        returnData := ptr
    }
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L88-L100

```solidity
129: assembly {
        mstore(array, moduleCount)
    }
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L129-L131





#### <ins>Recommended Mitigation Steps</ins>

Consider upgrading to at least solidity v0.8.15.



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```solidity
15: address _implementation

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L15



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.



### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```solidity
108: (bool success,) = payable(msg.sender).call{value : missingAccountFunds, gas : type(uint256).max}("");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L108

```solidity
261: (bool success,) = receiver.call{value: payment}("");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L261

```solidity
285: (bool success,) = receiver.call{value: payment}("");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L285

```solidity
451: (bool success,) = dest.call{value:amount}("");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L451

```solidity
478: (bool success, bytes memory result) = target.call{value : value}(data);
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L478

```solidity
527: (bool req,) = address(entryPoint()).call{value : msg.value}("");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L527

```solidity
37: (bool success,) = beneficiary.call{value : amount}("");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L37

```solidity
176: (bool success,bytes memory result) = address(mUserOp.sender).call{gas : mUserOp.callGasLimit}(callData);
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L176

```solidity
106: (bool success,) = withdrawAddress.call{value : stake}("");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L106

```solidity
120: (bool success,) = withdrawAddress.call{value : withdrawAmount}("");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L120



#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
6: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
16: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L5-L16

```solidity
7: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L7

```solidity
7: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L7

```solidity
4: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol#L4

```solidity
6: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
7: import "@openzeppelin/contracts/proxy/utils/Initializable.sol";
8: import "@openzeppelin/contracts/utils/Address.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L6-L8



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.



### <a href="#Summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> Remove unused code
This code is not used in the main project contract files, remove it or add event-emit.
Code that is not in use, suggests that they should not be present and could potentially contain insecure functionalities.

#### <ins>Proof Of Concept</ins>


```solidity
function delegateCall
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L29

```solidity
function callAndRevert
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L57

```solidity
function _getImplementation
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L20

```solidity
function ceilDiv
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L45






### <a href="#Summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> `require()` Should Be Used Instead Of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its <a href="https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require">documentation</a> states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

#### <ins>Proof Of Concept</ins>


```solidity
13: assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("biconomy.scw.proxy.implementation")) - 1));

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/common/Singleton.sol#L13





### <a href="#Summary">[LOW&#x2011;10]</a><a name="LOW&#x2011;10"> Unused `receive()` Function Will Lock Ether In Contract 

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

#### <ins>Proof Of Concept</ins>


```solidity
550: receive() external payable {}
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550



#### <ins>Recommended Mitigation Steps</ins>

The function should call another function, otherwise it should revert


### <a href="#Summary">[LOW&#x2011;11]</a><a name="LOW&#x2011;11"> Missing Non Reentrancy Modifiers

The following functions are missing reentrancy modifier although some other public/external functions does use reentrancy modifer. Even though I did not find a way to exploit it, it seems like those functions should have the nonReentrant modifier as the other functions have it as well.

#### <ins>Proof Of Concept</ins>


```solidity
function execTransaction(
        Transaction memory _tx,
        uint256 batchId,
        FeeRefund memory refundInfo,
        bytes memory signatures
    ) public payable virtual override returns (bool success) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L192

```solidity
function handlePaymentRevert(
        uint256 gasUsed,
        uint256 baseGas,
        uint256 gasPrice,
        uint256 tokenGasPriceFactor,
        address gasToken,
        address payable refundReceiver
    ) external returns (uint256 payment) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L271

```solidity
function requiredTxGas(
        address to,
        uint256 value,
        bytes calldata data,
        Enum.Operation operation
    ) external returns (uint256) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L363

```solidity
function addDeposit() public payable {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L525

```solidity
function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts\smart-contract-wallet\SmartAccount.sol#L536



#### <ins>Recommended Mitigation Steps</ins>
Add reentrancy modifiers


## Non Critical Issues




### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
109: function setOwner(address _newOwner) external mixedAuth {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109

```solidity
24: function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L24

```solidity
26: function setFallbackHandler(address handler) public authorized {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L26

```solidity
20: function setupModules(address to, bytes memory data) internal {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L20

```solidity
24: function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24

```solidity
65: function setSigner( address _newVerifyingSigner) external onlyOwner{
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.




### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Function creates dirty bits

This explanation should be added in the NatSpec comments of this function that sends ether with call;

Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.
Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer.

#### <ins>Proof Of Concept</ins>


```solidity
21: function createSender(bytes calldata initCode) external returns (address sender) {
        address initAddress = address(bytes20(initCode[0 : 20]));
        bytes memory initCallData = initCode[20 :];
        bool success;
        
        assembly {
            success := call(gas(), initAddress, 0, add(initCallData, 0x20), mload(initCallData), 0, 32)
            sender := mload(0)
        }
        if (!success) {
            sender = address(0);
        }
    }
}

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L21

```solidity
15: function call(
        address to,
        uint256 value,
        bytes memory data,
        uint256 txGas
    ) internal returns (bool success) {
        assembly {
            success := call(txGas, to, value, add(data, 0x20), mload(data), 0, 0)
        }
    }

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/utils/Exec.sol#L15

```solidity
28: function execute(
        address to,
        uint256 value,
        bytes memory data,
        Enum.Operation operation,
        uint256 txGas
    ) internal returns (bool success) {
        if (operation == Enum.Operation.DelegateCall) {
            
            assembly {
                success := delegatecall(txGas, to, add(data, 0x20), mload(data), 0, 0)
            }
        } else {
            
            assembly {
                success := call(txGas, to, value, add(data, 0x20), mload(data), 0, 0)
            }
        }
        
        if (success) emit ExecutionSuccess(to, value, data, operation, txGas);
        else emit ExecutionFailure(to, value, data, operation, txGas);
    }
    
}

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L28

```solidity
45: function setFallbackHandler(address handler) public authorized {
        internalSetFallbackHandler(handler);
        emit ChangedFallbackHandler(handler);
    }

    
    fallback() external {
        bytes32 slot = FALLBACK_HANDLER_STORAGE_SLOT;
        
        assembly {
            let handler := sload(slot)
            if iszero(handler) {
                return(0, 0)
            }
            calldatacopy(0, 0, calldatasize())
            
            
            mstore(calldatasize(), shl(96, caller()))
            
            let success := call(gas(), handler, 0, 0, add(calldatasize(), 20), 0, 0)
            returndatacopy(0, 0, returndatasize())
            if iszero(success) {
                revert(0, returndatasize())
            }
            return(0, returndatasize())
        }
    }
}

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L45

```solidity
22: function transferToken(
        address token,
        address receiver,
        uint256 amount
    ) internal returns (bool transferred) {
        
        
        bytes memory data = abi.encodeWithSelector(0xa9059cbb, receiver, amount);
        
        assembly {
            
            
            let success := call(sub(gas(), 10000), token, 0, add(data, 0x20), mload(data), 0, 0x20)
            switch returndatasize()
                case 0 {
                    transferred := success
                }
                case 0x20 {
                    transferred := iszero(or(iszero(success), iszero(mload(0))))
                }
                default {
                    transferred := 0
                }
        }
    }
}

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/common/SecuredTokenTransfer.sol#L22

```solidity
53: function multiSend(bytes memory transactions) public payable {
        require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");
        
        assembly {
            let length := mload(transactions)
            let i := 0x20
            for {
                
            } lt(i, length) {
                
            } {
                
                
                
                let operation := shr(0xf8, mload(add(transactions, i)))
                
                
                let to := shr(0x60, mload(add(transactions, add(i, 0x01))))
                
                let value := mload(add(transactions, add(i, 0x15)))
                
                let dataLength := mload(add(transactions, add(i, 0x35)))
                
                let data := add(transactions, add(i, 0x55))
                let success := 0
                switch operation
                    case 0 {
                        success := call(gas(), to, value, data, dataLength, 0, 0)
                    }
                    case 1 {
                        success := delegatecall(gas(), to, data, dataLength, 0, 0)
                    }
                if eq(success, 0) {
                    revert(0, 0)
                }
                
                i := add(i, add(0x55, dataLength))
            }
        }
    }
}

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L53

```solidity
47: function multiSend(bytes memory transactions) public payable {
        
        assembly {
            let length := mload(transactions)
            let i := 0x20
            for {
                
            } lt(i, length) {
                
            } {
                
                
                
                let operation := shr(0xf8, mload(add(transactions, i)))
                
                
                let to := shr(0x60, mload(add(transactions, add(i, 0x01))))
                
                let value := mload(add(transactions, add(i, 0x15)))
                
                let dataLength := mload(add(transactions, add(i, 0x35)))
                
                let data := add(transactions, add(i, 0x55))
                let success := 0
                switch operation
                    case 0 {
                        success := call(gas(), to, value, data, dataLength, 0, 0)
                    }
                    
                    case 1 {
                        revert(0, 0)
                    }
                if eq(success, 0) {
                    revert(0, 0)
                }
                
                i := add(i, add(0x55, dataLength))
            }
        }
    }
}

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSendCallOnly.sol#L47




#### <ins>Recommended Mitigation Steps</ins>
Add this comment to the function:
```
/// @dev Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer. Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.
```



### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Function writing that does not comply with the Solidity Style Guide

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

#### <ins>Proof Of Concept</ins>

All in-scope contracts




### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Use `delete` to Clear Variables

`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at `x`.

The `delete` key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with `delete` statements.

#### <ins>Proof Of Concept</ins>

```solidity
106: opIndex = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L106

```solidity
102: info.unstakeDelaySec = 0;
103: info.withdrawTime = 0;
104: info.stake = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L102-L104





### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> No need to initialize uints to zero

There is no need to initialize `uint` variables to zero as their default value is `0`

#### <ins>Proof Of Concept</ins>

```solidity
234: uint256 payment = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L234

```solidity
310: uint256 i = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L310

```solidity
78: uint256 collected = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L78

```solidity
99: uint256 totalOps = 0;
106: uint256 opIndex = 0;
126: uint256 collected = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L99

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L106

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L126



```solidity
313: uint256 missingAccountFunds = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L313

```solidity
119: uint256 moduleCount = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L119

```solidity
259: uint256 result = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L259

```solidity
310: uint256 result = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L310

```solidity
310: uint256 result = 0;

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/Math.sol#L310





### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### <ins>Proof Of Concept</ins>

```solidity
46: //     "AccountTx(address to,uint256 value,bytes data,uint8 operation,uint256 targetTxGas,uint256 baseGas,uint256 gasPrice,address gasToken,address refundReceiver,uint256 nonce)"
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L46

```solidity
186: /// @dev Allows to execute a Safe transaction confirmed by required number of owners and then pays the account that submitted the transaction.
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L186

```solidity
357: ///      Since the `estimateGas` function includes refunds, call this method to get an estimated of the costs that are deducted from the safe with `execTransaction`
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L357





### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


```solidity
24: function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L24

```solidity
20: function setupModules(address to, bytes memory data) internal {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L20

```solidity
24: function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L24

```solidity
65: function setSigner( address _newVerifyingSigner) external onlyOwner{
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L65





### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```solidity
15: constructor(address _implementation)
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol#L15

```solidity
17: constructor(address _baseImpl)
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L17

```solidity
20: constructor(IEntryPoint _entryPoint)
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L20

```solidity
12: constructor()
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol#L12

```solidity
20: constructor(IEntryPoint _entryPoint)
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/BasePaymaster.sol#L20

```solidity
35: constructor(IEntryPoint _entryPoint, address _verifyingSigner) BasePaymaster(_entryPoint)
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L35





### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
10: import "./common/Enum.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L10

```solidity
4: import "./libs/LibAddress.sol";
8: import "./BaseSmartAccount.sol";
9: import "./common/Singleton.sol";
10: import "./base/ModuleManager.sol";
11: import "./base/FallbackManager.sol";
12: import "./common/SignatureDecoder.sol";
13: import "./common/SecuredTokenTransfer.sol";
14: import "./interfaces/ISignatureValidator.sol";
15: import "./interfaces/IERC165.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L4-L15



```solidity
4: import "./Proxy.sol";
5: import "./BaseSmartAccount.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L4-L5

```solidity
8: import "../interfaces/IPaymaster.sol";
9: import "../interfaces/IEntryPoint.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/BasePaymaster.sol#L8-L9

```solidity
12: import "../interfaces/IAccount.sol";
13: import "../interfaces/IPaymaster.sol";
15: import "../interfaces/IAggregatedAccount.sol";
16: import "../interfaces/IEntryPoint.sol";
17: import "../utils/Exec.sol";
18: import "./StakeManager.sol";
19: import "./SenderCreator.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L12-L19



```solidity
4: import "../interfaces/IStakeManager.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L4

```solidity
4: import "./UserOperation.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L4

```solidity
4: import "./UserOperation.sol";
5: import "./IAccount.sol";
6: import "./IAggregator.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L4-L6

```solidity
4: import "./UserOperation.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregator.sol#L4

```solidity
12: import "./UserOperation.sol";
13: import "./IStakeManager.sol";
14: import "./IAggregator.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L12-L14

```solidity
4: import "./UserOperation.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L4

```solidity
4: import "../common/Enum.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/Executor.sol#L4

```solidity
4: import "../common/SelfAuthorized.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/FallbackManager.sol#L4

```solidity
4: import "../common/Enum.sol";
5: import "../common/SelfAuthorized.sol";
6: import "./Executor.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L4-L6

```solidity
4: import "../interfaces/ERC1155TokenReceiver.sol";
5: import "../interfaces/ERC721TokenReceiver.sol";
6: import "../interfaces/ERC777TokensRecipient.sol";
7: import "../interfaces/IERC165.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L4-L7

```solidity
5: import "../../BasePaymaster.sol";
9: import "../../PaymasterHelpers.sol";
10: import "../samples/Signatures.sol";

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L5-L10






#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Use a more recent version of Solidity


<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

#### <ins>Proof Of Concept</ins>

All in-scope contracts



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Open TODOs

An open TODO is present. It is recommended to avoid open TODOs as they may indicate programming errors that still need to be fixed.

#### <ins>Proof Of Concept</ins>


```solidity
255: // TODO: copy logic of gasPrice?
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L255






### <a href="#Summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
294: revert(string(abi.encodePacked(requiredGas)
374: revert(string(abi.encodePacked(requiredGas)
347: _signer = ecrecover(keccak256(abi.encodePacked("/x19Ethereum Signed Message:/n32", dataHash)
294: revert(string(abi.encodePacked(requiredGas)
374: revert(string(abi.encodePacked(requiredGas)
445: return abi.encodePacked(bytes1(0x19)

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L294

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L374

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L347

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L294

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L374

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L445



```solidity
34: bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index)
70: bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index)
35: bytes memory deploymentData = abi.encodePacked(type(Proxy)
54: bytes memory deploymentData = abi.encodePacked(type(Proxy)
35: bytes memory deploymentData = abi.encodePacked(type(Proxy)
54: bytes memory deploymentData = abi.encodePacked(type(Proxy)
69: bytes memory code = abi.encodePacked(type(Proxy)
34: bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index)
70: bytes32 salt = keccak256(abi.encodePacked(_owner, address(uint160(_index)
71: bytes32 hash = keccak256(abi.encodePacked(bytes1(0xff)

```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L34

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L70

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L35

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L54

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L35

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L54

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L69

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L34

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L70

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L71





#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#Summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```solidity
321: if (_deadline != 0 && _deadline < block.timestamp) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L321

```solidity
365: if (_deadline != 0 && _deadline < block.timestamp) {
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L365

```solidity
101: require(info.withdrawTime <= block.timestamp, "Stake withdrawal is not due");
```

https://github.com/code-423n4/2023-01-biconomy/tree/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L101



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.


### <a href="#Summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> Commented Code

Commented code is exposed throughout all in-scope contracts which suggest unfinished code. It is recommended to remove commented code for finalized contracts.

#### <ins>Proof Of Concept</ins>

All in-scope contracts.

i.e. Such as SmartAccount.sol
```solidity
192: function execTransaction(
        Transaction memory _tx,
        uint256 batchId,
        FeeRefund memory refundInfo,
        bytes memory signatures
    ) public payable virtual override returns (bool success) {
        // initial gas = 21k + non_zero_bytes * 16 + zero_bytes * 4
        //            ~= 21k + calldata.length * [1/3 * 16 + 2/3 * 4]
        uint256 startGas = gasleft() + 21000 + msg.data.length * 8;
        //console.log("init %s", 21000 + msg.data.length * 8);
        bytes32 txHash;
        // Use scope here to limit variable lifetime and prevent `stack too deep` errors
        {
            bytes memory txHashData =
                encodeTransactionData(
                    // Transaction info
                    _tx,
                    // Payment info
                    refundInfo,
                    // Signature info
                    nonces[batchId]
                );
            // Increase nonce and execute transaction.
            // Default space aka batchId is 0
            nonces[batchId]++;
            txHash = keccak256(txHashData);
            checkSignatures(txHash, txHashData, signatures);
        }


        // We require some gas to emit the events (at least 2500) after the execution and some to perform code until the execution (500)
        // We also include the 1/64 in the check that is not send along with a call to counteract potential shortings because of EIP-150
        require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
        // Use scope here to limit variable lifetime and prevent `stack too deep` errors
        {
            // If the gasPrice is 0 we assume that nearly all available gas can be used (it is always more than targetTxGas)
            // We only substract 2500 (compared to the 3000 before) to ensure that the amount passed is still higher than targetTxGas
            success = execute(_tx.to, _tx.value, _tx.data, _tx.operation, refundInfo.gasPrice == 0 ? (gasleft() - 2500) : _tx.targetTxGas);
            // If no targetTxGas and no gasPrice was set (e.g. both are 0), then the internal tx is required to be successful
            // This makes it possible to use `estimateGas` without issues, as it searches for the minimum gas where the tx doesn't revert
            require(success || _tx.targetTxGas != 0 || refundInfo.gasPrice != 0, "BSA013");
            // We transfer the calculated tx costs to the tx.origin to avoid sending it to intermediate contracts that have made calls
            uint256 payment = 0;
            // uint256 extraGas;
            if (refundInfo.gasPrice > 0) {
                //console.log("sent %s", startGas - gasleft());
                // extraGas = gasleft();
                payment = handlePayment(startGas - gasleft(), refundInfo.baseGas, refundInfo.gasPrice, refundInfo.tokenGasPriceFactor, refundInfo.gasToken, refundInfo.refundReceiver);
                emit WalletHandlePayment(txHash, payment);
            }
            // extraGas = extraGas - gasleft();
            //console.log("extra gas %s ", extraGas);
        }
    }
```

### <a href="#Summary">[NC&#x2011;15]</a><a name="NC&#x2011;15"> Missing/In-complete NATSPEC

Missing NATSPEC documentation through many of the in-scope contracts

#### <ins>Proof Of Concept</ins>

All in-scope contracts.