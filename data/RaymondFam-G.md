## Private function with embedded modifier reduces contract size
Consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A private visibility that saves more gas on function calls than the internal visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows just as it has been implemented in [SelfAuthorized.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/common/SelfAuthorized.sol):

[File: SmartAccount.sol#L82-L85](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L82-L85)

```diff
+    function _mixedAuth() private view {
+        require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
+    }

   modifier mixedAuth {
-        require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
+        _mixedAuth();
       _;
   }
```
## Unneeded `uint256()` cast
Casting an unsigned integer to `uint256()` is unnecessary. 

For instance, the code line below may be refactored to save gas both on contract deployment and function call as follows:

[File: SmartAccount.sol#L322](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L322)

```diff
-              require(uint256(s) >= uint256(1) * 65, "BSA021");
+              require(uint256(s) >= 65, "BSA021");
```
Note: Since `1 * 65 == 65`, replacing `uint256(1) * 65` with `65` instead of `1 * 65` saves even more gas.

## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: SmartAccountNoAuth.sol#L155-L159](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccountNoAuth.sol#L155-L159)

```diff
    function getNonce(uint256 batchId)
    public view
-    returns (uint256) {
+    returns (uint256 _2dNonce) {
-        return nonces[batchId];
+        _2dNonce = nonces[batchId];
    }
```
## Check logic that could lead to waste of gas
In ModuleManager.sol, `getModulesPaginated()` has `currentModule != address(0x0)` included in the `while` statement. If `start` was inputted as a non-existent key, `(currentModule == address(0x0)`. This would skip the `while` loop and move on to executing the rest of the code lines while irrelevantly and meaninglessly returning the output.

Consider separating the zero address check by having the function refactored as follows so that a revert could stem unneeded code executions coming after it:

[File: ModuleManager.sol#L114-L132](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L114-L132)

```diff
    function getModulesPaginated(address start, uint256 pageSize) external view returns (address[] memory array, address next) {
        // Init array with max page size
        array = new address[](pageSize);

        // Populate return array
        uint256 moduleCount = 0;
        address currentModule = modules[start];
+        require (currentModule != address(0x0), "BSA105"); // change to a different revert message where deemed fit
-        while (currentModule != address(0x0) && currentModule != SENTINEL_MODULES && moduleCount < pageSize) {
+        while (currentModule != SENTINEL_MODULES && moduleCount < pageSize) {
            array[moduleCount] = currentModule;
            currentModule = modules[currentModule];
            moduleCount++;
        }
        next = currentModule;
        // Set correct size of returned array
        // solhint-disable-next-line no-inline-assembly
        assembly {
            mstore(array, moduleCount)
        }
    }
```
## Payable access control functions costs less gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`.

For instance, the code line below may be refactored as follows:

[File: SmartAccount.sol#L449](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449)

```diff
-    function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
+    function transfer(address payable dest, uint amount) external payable nonReentrant onlyOwner {
```
## Redundant checks
`init()` in SmartAccount.sol has `_handler != address(0)` unnecessarily included twice in its function logic. Consider removing the second identical check to save gas both on contract deployment and function calls:

[File: SmartAccount.sol#L166-L176](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166-L176)

```diff
    function init(address _owner, address _entryPointAddress, address _handler) public override initializer { 
        require(owner == address(0), "Already initialized");
        require(address(_entryPoint) == address(0), "Already initialized");
        require(_owner != address(0),"Invalid owner");
        require(_entryPointAddress != address(0), "Invalid Entrypoint");
        require(_handler != address(0), "Invalid Entrypoint");
        owner = _owner;
        _entryPoint =  IEntryPoint(payable(_entryPointAddress));
-        if (_handler != address(0)) internalSetFallbackHandler(_handler);
+        internalSetFallbackHandler(_handler);
        setupModules(address(0), bytes(""));
    }
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, consider replacing `>=` with the strict counterpart `>` in the following inequality instance:

[File: SmartAccount.sol#L322](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L322)

```diff
-              require(uint256(s) >= uint256(1) * 65, "BSA021");
+              require(uint256(s) > uint256(1) * 65 - 1, "BSA021");
```
## Early checks
In BaseSmartAccount.sol, `(missingAccountFunds != 0)` should be prepended before `_payPrefund()` is invoked in `validateUserOp()` instead of being included in `_payPrefund()`. This will make the external function call revert earlier to save some gas if need be.

[File: BaseSmartAccount.sol#L60-L68](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L60-L68)

```diff
    function validateUserOp(UserOperation calldata userOp, bytes32 userOpHash, address aggregator, uint256 missingAccountFunds)
    external override virtual returns (uint256 deadline) {
        _requireFromEntryPoint();
        deadline = _validateSignature(userOp, userOpHash, aggregator);
        if (userOp.initCode.length == 0) {
            _validateAndUpdateNonce(userOp);
        }
+        if (missingAccountFunds != 0) {
            _payPrefund(missingAccountFunds);
+        }
    }
```
[File: BaseSmartAccount.sol#L106-L112](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol#L106-L112)

```diff
    function _payPrefund(uint256 missingAccountFunds) internal virtual {
-        if (missingAccountFunds != 0) {
            (bool success,) = payable(msg.sender).call{value : missingAccountFunds, gas : type(uint256).max}("");
-            (success);
            //ignore failure (its EntryPoint's job to verify, not account.)
-        }
    }
```
Since `success` isn't going to be checked, it may also be removed from the code block to save gas both on contract deployment and function call.

## Unchecked SafeMath saves gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ... }` to use the previous wrapping behavior further saves gas just as in the instance below as an example:

[File: SmartAccount.sol#L216](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L216)

```diff
+    unchecked {
            nonces[batchId]++;
+    }
```
## Inline over internal
Internal function entailing only one line of code may be embedded in the calling function(s) to save gas.

Here are some of the instances entailed:

[File: SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

```
461:        _requireFromEntryPointOrOwner();

466:        _requireFromEntryPointOrOwner();

494:    function _requireFromEntryPointOrOwner() internal view {
495:        require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
496:    }
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
version: "0.8.12",
settings: {
  optimizer: {
    enabled: true,
    runs: 1000,
  },
},
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Split require statements using &&
Instead of using the `&&` operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`.

Here are some of the instances entailed:

[File: ModuleManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol)

```
34:        require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

49:        require(module != address(0) && module != SENTINEL_MODULES, "BSA101");

68:        require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```
## Use storage instead of memory for structs/arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct.

Here are some of the instances entailed:

[File: EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)

```
171:        MemoryUserOp memory mUserOp = opInfo.mUserOp;

229:        UserOpInfo memory outOpInfo;

234:        StakeInfo memory paymasterInfo = getStakeInfo(outOpInfo.mUserOp.paymaster);
235:        StakeInfo memory senderInfo = getStakeInfo(outOpInfo.mUserOp.sender);

238:        StakeInfo memory factoryInfo = getStakeInfo(factory);

241:            AggregatorStakeInfo memory aggregatorInfo = AggregatorStakeInfo(aggregator, getStakeInfo(aggregator));

293:        MemoryUserOp memory mUserOp = opInfo.mUserOp;

351:        MemoryUserOp memory mUserOp = opInfo.mUserOp;

359:        MemoryUserOp memory mUserOp = outOpInfo.mUserOp;

444:        MemoryUserOp memory mUserOp = opInfo.mUserOp;
```
## += and -= cost more gas
`+=` and `-=` generally cost 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

For instance, the `+=` instance below may be refactored as follows:

[File: EntryPoint.sol#L81](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L81)

```diff
-            collected += _executeUserOp(i, ops[i], opInfos[i]);
+            collected = collected + _executeUserOp(i, ops[i], opInfos[i]);
```
