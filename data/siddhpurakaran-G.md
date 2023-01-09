## [G-01] USE FUNCTION INSTEAD OF MODIFIERS (3 INSTANCES)
Functions saves gas as compared to modifiers.
Example : 
```
-----------------------Before-----------------------------------
   modifier onlyMinters() {
      require(minters[msg.sender] == true, "Only minters");
      _;
   }
  
   function minter_mint(address m_address, uint256 m_amount) public onlyMinters {
        onlyMinters();
            super._mint(m_address, m_amount);
    }

---------------------------After---------------------------------
  function onlyMinters() private {
      require(minters[msg.sender] == true, "Only minters");
      _;
   }

  function minter_mint(address m_address, uint256 m_amount) public {
       onlyMinters();
       super._mint(m_address, m_amount);
  }

-------------------------------------------------------------------------
```

# 1. contracts/smart-contract-wallet/SmartAccount.sol 
```
     L76       modifier onlyOwner {
     L82       modifier mixedAuth {
     L88      modifier onlyEntryPoint {
```


## [G-02] STORAGE POINTER TO A STRUCTURE IS CHEAPER THAN COPYING EACH VALUE OF THE STRUCTURE INTO MEMORY, SAME FOR ARRAY AND MAPPING (16 INSTANCE)
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

# 1. contracts/smart-contract-wallet/SmartAccount.sol 
```
     L402           Transaction memory _tx = Transaction({
     L409           FeeRefund memory refundInfo = FeeRefund({
```

# 2. contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol
```
     L71           UserOpInfo[] memory opInfos = new UserOpInfo[](opslen);
     L104           UserOpInfo[] memory opInfos = new UserOpInfo[](totalOps);
     L171        MemoryUserOp memory mUserOp = opInfo.mUserOp;
     L229          UserOpInfo memory outOpInfo;
     L234                StakeInfo memory paymasterInfo = getStakeInfo(outOpInfo.mUserOp.paymaster);
     L235           StakeInfo memory senderInfo = getStakeInfo(outOpInfo.mUserOp.sender);
     L238           StakeInfo memory factoryInfo = getStakeInfo(factory);
     L241               AggregatorStakeInfo memory aggregatorInfo = AggregatorStakeInfo(aggregator, getStakeInfo(aggregator));
     L293           MemoryUserOp memory mUserOp = opInfo.mUserOp;
     L351        MemoryUserOp memory mUserOp = opInfo.mUserOp;
     L389           MemoryUserOp memory mUserOp = outOpInfo.mUserOp;
     L444        MemoryUserOp memory mUserOp = opInfo.mUserOp;
```

 # 3. contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol   
```
    L102        PaymasterData memory paymasterData = userOp.decodePaymasterData();
    L126        PaymasterContext memory data = context.decodePaymasterContext();
```        

 ## [G-03] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, FOR EXAMPLE WHEN USED IN FOR- AND WHILE-LOOPS  (8 INSTANCE)
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

 # 1. contracts/smart-contract-wallet/base/ModuleManager.sol
```
    L124            moduleCount++;
```

 # 2. contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol        
```
    L100         for (uint256 i = 0; i < opasLen; i++) {
    L107         for (uint256 a = 0; a < opasLen; a++) {
    L112             for (uint256 i = 0; i < opslen; i++) {
    L114                 opIndex++;
    L128         for (uint256 a = 0; a < opasLen; a++) {
    L134             for (uint256 i = 0; i < opslen; i++) {
    L136                 opIndex++;
```
        

 ## [G-04] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS  (3 INSTANCES)   
Saves 16 gas per instance. If you’re using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas
        
 # 1. contracts/smart-contract-wallet/base/ModuleManager.sol        
```
    L34            require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
    L49            require(module != address(0) && module != SENTINEL_MODULES, "BSA101");
    L68            require(msg.sender != SENTINEL_MODULES && modules[msg.sender] != address(0), "BSA104");
```

 ## [G-05] USE BYTES32 INSTEAD OF STRING  (4 INSTANCES) 
Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

 # 1. contracts/smart-contract-wallet/SmartAccount.sol
```
    L36       string public constant VERSION = "1.0.2"; // using AA 0.3.0
     
```

 # 2. contracts/smart-contract-wallet/SmartAccountFactory.sol
```
    L11    string public constant VERSION = "1.0.2";
```

 # 3. contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol     
```
    L12    string public constant NAME = "Default Callback Handler";
    L13    string public constant VERSION = "1.0.0";
```

 ##  [G-06] ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()   (4 INSTANCES)       
If possible use abi.encodePacked() instead of abi.encode() to save gas.

 # 1. contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
```
    L80         return keccak256(abi.encode(
                userOp.getSender(),
                userOp.nonce,
                keccak256(userOp.initCode),
                keccak256(userOp.callData),
                userOp.callGasLimit,
                userOp.verificationGasLimit,
                userOp.preVerificationGas,
                userOp.maxFeePerGas,
                userOp.maxPriorityFeePerGas
            ));

```

 # 2. contracts/smart-contract-wallet/paymasters/PaymasterHelpers.sol
```
    L28        return abi.encode(data.paymasterId);
```

 # 3. contracts/smart-contract-wallet/SmartAccount.sol
```
    L136           return keccak256(abi.encode(DOMAIN_SEPARATOR_TYPEHASH, getChainId(), this)); 
    L431               keccak256(
                abi.encode(
                    ACCOUNT_TX_TYPEHASH,
                    _tx.to,
                    _tx.value,
                    keccak256(_tx.data),
                    _tx.operation,
                    _tx.targetTxGas,
                    refundInfo.baseGas,
                    refundInfo.gasPrice,
                    refundInfo.gasToken,
                    refundInfo.refundReceiver,
                    _nonce
                )
            );
```


 ## [G-07] USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT IN A REQUIRE() STATEMENT  (17 INSTANCES)       
When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance.

 # 1. contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol
```
    L61         require(_unstakeDelaySec > 0, "must specify unstake delay");
    L64         require(stake > 0, "no stake specified");
    L99         require(stake > 0, "No stake to withdraw");
```

 # 2. contracts/smart-contract-wallet/libs/Math.sol
```
     L147         if (rounding == Rounding.Up && mulmod(x, y, denominator) > 0) {
    L208             if (value >> 128 > 0) {
    L212             if (value >> 64 > 0) {
    L216             if (value >> 32 > 0) {
    L220             if (value >> 16 > 0) {
    L224             if (value >> 8 > 0) {
    L228             if (value >> 4 > 0) {
    L232             if (value >> 2 > 0) {
    L236            if (value >> 256 > 0) {
    L312             if (value >> 128 > 0) {
    L316             if (value >> 64 > 0) {
    L320             if (value >> 32 > 0) {
    L324             if (value >> 16 > 0) {
    L328            if (value >> 8 > 0) {
 ```

 ## [G-08] X = X + Y IS MORE EFFICIENT, THAN X += Y (40 INSTANCES)        
use = + or = - instead to save gas.
        
 # 1. contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol
```
     L81                    collected += _executeUserOp(i, ops[i], opInfos[i]);
     L101               totalOps += opsPerAggregator[i].userOps.length;
     L135                   collected += _executeUserOp(opIndex, ops[i], opInfos[opIndex]);
     L468        actualGas += preGas - gasleft();
```

 # 2. contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol
```
    L51          paymasterIdBalances[paymasterId] += msg.value;
    L58          paymasterIdBalances[msg.sender] -= amount;
    L128       paymasterIdBalances[extractedPaymasterId] -= actualGasCost;
```
    
 # 3. contracts/smart-contract-wallet/libs/Math.sol
```
    L121             inverse *= 2 - denominator * inverse; // inverse mod 2^8
    L122             inverse *= 2 - denominator * inverse; // inverse mod 2^16
    L123              inverse *= 2 - denominator * inverse; // inverse mod 2^32
    L124              inverse *= 2 - denominator * inverse; // inverse mod 2^64
    L125              inverse *= 2 - denominator * inverse; // inverse mod 2^128
    L126              inverse *= 2 - denominator * inverse; // inverse mod 2^256
    L148              result += 1;
    L210                  result += 128;
    L214                  result += 64;
    L218                  result += 32;
    L222                  result += 16;
    L226                  result += 8;
    L230                  result += 4;
    L234                  result += 2;
    L237                  result += 1;
    L262                  value /= 10**64;
    L263                  result += 64;
    L266                  value /= 10**32;
    L267                  result += 32;
    L270                  value /= 10**16;
    L271                  result += 16;
    L274                  value /= 10**8;
    L275                  result += 8;
    L278                  value /= 10**4;
    L279                  result += 4;
    L282                  value /= 10**2;
    L283                  result += 2;
    L286                  result += 1;
    L314                  result += 16;
    L318                  result += 8;
    L322                   result += 4;
    L326                  result += 2;
    L329                result += 1;
```    
