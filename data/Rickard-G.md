# STATE VARIABLES CAN BE PACKED INTO FEWER STORAGE SLOTS
BaseSmartAccount.sol
```
struct Transaction {
        	address to;
-      	uint256 value;
        	bytes data;
        	Enum.Operation operation;
+      	uint256 value;
        	uint256 targetTxGas;
    	}

```
```
struct FeeRefund {
+      	address gasToken;
        	uint256 baseGas;
        	uint256 gasPrice; //gasPrice or tokenGasPrice
        	uint256 tokenGasPriceFactor;
-       	address gasToken;
        	address payable refundReceiver;
    	}

```
EntryPoint.sol
```
    	struct MemoryUserOp {
        		address sender;
+		address paymaster;
       	 	uint256 nonce;
       		uint256 callGasLimit;
       		uint256 verificationGasLimit;
       		uint256 preVerificationGas;
-		address paymaster;
        		uint256 maxFeePerGas;
       		uint256 maxPriorityFeePerGas;
    		}
```
```
    		struct DepositInfo {
-		uint112 deposit;
-		bool staked;
-		uint112 stake;
        		uint32 unstakeDelaySec;
        		uint64 withdrawTime;
+		uint112 stake;
+		uint112 deposit;
+		bool staked;
    		}
```
# BYTES CONSTANTS ARE MORE EFFICIENT THAN STRING CONSTANTS
If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.
[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L36)
```
    string public constant VERSION = "1.0.2"; // using AA 0.3.0
```
[SmartAccountFactory.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccountFactory.sol#L11)
```
    string public constant VERSION = "1.0.2";
```
DefaultCallbackHandler.sol [L12](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L12), [L13](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L13)
```
12     	string public constant NAME = "Default Callback Handler";
13    	string public constant VERSION = "1.0.0";
```
# REPLACE MODIFIER WITH FUNCTION
SmartAccount.sol: [L76](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L76)
```
76	-	modifier onlyOwner {
76	+	function onlyOwner() private view {
77        		require(msg.sender == owner, "Smart Account:: Sender is not authorized");
78	-	 	_;
79   		}
80
81   		// onlyOwner OR self
82	-	modifier mixedAuth {
82	+	function mixedAuth() private view {
83        		require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
84	-	 	_;
85   		}
86
87  		// only from EntryPoint
88	-	modifier onlyEntryPoint {
88	+	function onlyEntryPoint() private view {
89       			require(msg.sender == address(entryPoint()), "wallet: not from EntryPoint");
90	-		_; 
91		}

109	-	function setOwner(address _newOwner) external mixedAuth {
109	+	function setOwner(address _newOwner) external {
+	 	mixedAuth();
110       		require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
111       		address oldOwner = owner;
112       		owner = _newOwner;
113        		emit EOAChanged(address(this), oldOwner, _newOwner);
114		}

120	-	function updateImplementation(address _implementation) external mixedAuth {
120	+	function updateImplementation(address _implementation) external {
+	  	mixedAuth()
121        		require(_implementation.isContract(), "INVALID_IMPLEMENTATION");
122        		_setImplementation(_implementation);
123        		// EOA + Version tracking
124        		emit ImplementationUpdated(address(this), VERSION, _implementation);
125		}

127	-	function updateEntryPoint(address _newEntryPoint) external mixedAuth {
127	+	function updateEntryPoint(address _newEntryPoint) external {
+	   	mixedAuth()
128         		require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
129         		emit EntryPointChanged(address(_entryPoint), _newEntryPoint);
130        		 _entryPoint = IEntryPoint(payable(_newEntryPoint));
131     		}

449	-	function transfer(address payable dest, uint amount) external nonReentrant onlyOwner {
449	+	function transfer(address payable dest, uint amount) external nonReentrant {
+		onlyOwner();
450        	 	require(dest != address(0), "this action will burn your funds");
451         		(bool success,) = dest.call{value:amount}("");
452         		require(success,"transfer failed");
453     		}

455	-	function pullTokens(address token, address dest, uint256 amount) external onlyOwner {
455	+	function pullTokens(address token, address dest, uint256 amount) external {
+	   	onlyOwner ();
456        		IERC20 tokenContract = IERC20(token);
457        		SafeERC20.safeTransfer(tokenContract, dest, amount);
458    		}

460	-	function execute(address dest, uint value, bytes calldata func) external onlyOwner{
460	+	function execute(address dest, uint value, bytes calldata func) external {
+	  	onlyOwner();
461       		 _requireFromEntryPointOrOwner();
462        		_call(dest, value, func);
463    		}

465	-	function executeBatch(address[] calldata dest, bytes[] calldata func) external onlyOwner{
465	+	function executeBatch(address[] calldata dest, bytes[] calldata func) external {
+	  	onlyOwner();
466       	 	_requireFromEntryPointOrOwner();
467        		require(dest.length == func.length, "wrong array lengths");
468       		 for (uint i = 0; i < dest.length;) {
469            			_call(dest[i], 0, func[i]);
470            			unchecked {
471                			++i;
472           	 		}
473        		}
474    		}

489	-	function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        
489	+	function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external returns (bool success) {  
+	  	onlyEntryPoint();
490        		success = execute(dest, value, func, operation, gasLimit);
491        		require(success, "Userop Failed");
492    		}

536	-	function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public onlyOwner {
536	+	function withdrawDepositTo(address payable withdrawAddress, uint256 amount) public {
+	  	onlyOwner(); 
537        		entryPoint().withdrawTo(withdrawAddress, amount);
538    		}
```
# FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USER CAN BE MARKED PAYABLE
If a function modifier() is used, the function will be reverted if called by normal user.
The extra opcodes avoided are `callValue`, `dup1`, `isZero`, `push2`, `jumpI`, `push1`, `dup1`, `revert`, `jumpdest`, `pop`.

SmartAccount.sol:
[L109-L114](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109-L114), [L120-L125](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L120-L125), [L127-L131](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L127-L131), [L449-L453](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L449-L453), [L455-L458](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L455-L458), [L460-L463](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L460-L463), [L465-L474](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L465-L474), [L489-L492](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489-L492), [L536-L538](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L536-L538)
# REQUIRE() / REVERT() THAT CHECK INPUT ARGUMENTS SHOULD BE AT THE TOP
Checks that involve constants should come before state variables checks, function calls and calculations.

[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L363-L375)
```
363    function requiredTxGas(
364         address to,
365         uint256 value,
366         bytes calldata data,
367         Enum.Operation operation
368         ) external returns (uint256) {
+             require(execute(to, value, data, operation, gasleft()));
369         uint256 startGas = gasleft();
370         // We don't provide an error message here, as we use it to return the estimate
-        require(execute(to, value, data, operation, gasleft()));
372         uint256 requiredGas = startGas - gasleft();
373         // Convert response to string and return via error message
374         revert(string(abi.encodePacked(requiredGas)));
375     }
```
EntryPoint.sol: [168-190](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L168-L190), [203-218](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L203-L218)
```
168    function innerHandleOp(bytes calldata callData, UserOpInfo memory opInfo, bytes calldata context) external returns (uint256 actualGasCost) {
+        require(msg.sender == address(this), "AA92 internal call only");
169         uint256 preGas = gasleft();
-         require(msg.sender == address(this), "AA92 internal call only");
171         MemoryUserOp memory mUserOp = opInfo.mUserOp;
172
173        IPaymaster.PostOpMode mode = IPaymaster.PostOpMode.opSucceeded;
174        if (callData.length > 0) {
175
176            (bool success,bytes memory result) = address(mUserOp.sender).call{gas : mUserOp.callGasLimit}(callData);
177            if (!success) {
178                if (result.length > 0) {
179                    emit UserOperationRevertReason(opInfo.userOpHash, mUserOp.sender, mUserOp.nonce, result);
180                }
181                mode = IPaymaster.PostOpMode.opReverted;
182            }
183        }
184
185    unchecked {
186        uint256 actualGas = preGas - gasleft() + opInfo.preOpGas;
187        //note: opIndex is ignored (relevant only if mode==postOpReverted, which is only possible outside of innerHandleOp)
188        return _handlePostOp(0, mode, opInfo, context, actualGas);
189    }
190    }

203    function _copyUserOpToMemory(UserOperation calldata userOp, MemoryUserOp memory mUserOp) internal pure {
+            require(paymasterAndData.length >= 20, "AA93 invalid paymasterAndData");
204        mUserOp.sender = userOp.sender;
205        mUserOp.nonce = userOp.nonce;
206        mUserOp.callGasLimit = userOp.callGasLimit;
207        mUserOp.verificationGasLimit = userOp.verificationGasLimit;
208        mUserOp.preVerificationGas = userOp.preVerificationGas;
209        mUserOp.maxFeePerGas = userOp.maxFeePerGas;
210        mUserOp.maxPriorityFeePerGas = userOp.maxPriorityFeePerGas;
211        bytes calldata paymasterAndData = userOp.paymasterAndData;
212        if (paymasterAndData.length > 0) {
-            require(paymasterAndData.length >= 20, "AA93 invalid paymasterAndData");
214            mUserOp.paymaster = address(bytes20(paymasterAndData[: 20]));
215        } else {
216            mUserOp.paymaster = address(0);
217        }
218    }
```
[StakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L59-L74)
```
59    function addStake(uint32 _unstakeDelaySec) public payable {
+        require(_unstakeDelaySec > 0, "must specify unstake delay");
+        require(_unstakeDelaySec >= info.unstakeDelaySec, "cannot decrease unstake time");
60        DepositInfo storage info = deposits[msg.sender];
-        require(_unstakeDelaySec > 0, "must specify unstake delay");
-        require(_unstakeDelaySec >= info.unstakeDelaySec, "cannot decrease unstake time");
63        uint256 stake = info.stake + msg.value;
64        require(stake > 0, "no stake specified");
65        require(stake < type(uint112).max, "stake overflow");
66        deposits[msg.sender] = DepositInfo(
67            info.deposit,
68            true,
69            uint112(stake),
70            _unstakeDelaySec,
71            0
72        );
73        emit StakeLocked(msg.sender, stake, _unstakeDelaySec);
74    }
```
# LINES ARE TOO LONG
Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.
[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L489)
```
    function execFromEntryPoint(address dest, uint value, bytes calldata func, Enum.Operation operation, uint256 gasLimit) external onlyEntryPoint returns (bool success) {        
```
# EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING
[SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L550)
```
    receive() external payable {}
```
# AVOID FLOATING SOLIDITY PRAGMAS (^)
- [EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L6)
- [SenderCreator.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/SenderCreator.sol#L2)
- [StakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L2)
- [IAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol#L2)
- [IAggregatedAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAggregatedAccount.sol#L2)
- [IEntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IEntryPoint.sol#L6)
- [IPaymaster.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IPaymaster.sol#L2)
- [IStakeManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IStakeManager.sol#L2)
- [UserOperation.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/UserOperation.sol#L2)
```
pragma solidity ^0.8.12;
```
# X = X + Y COSTS LESS THAN X += Y
[EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L80)
```
80        for (uint256 i = 0; i < opslen; i++) {
101      totalOps += opsPerAggregator[i].userOps.length;
135      collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);
468      actualGas += preGas - gasleft();
```
# SPLITTING REQUIRE() STATEMENTS THAT USE &&
[ModuleManager.sol](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34)
```
34        require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
49        require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
68        require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
106      return SENTINEL_MODULES != module && modules[module] != address(0);
```




