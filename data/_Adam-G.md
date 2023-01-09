#### [G01] Unchecked increments in for loops
**Description:**
When incrementing i in for loops there is no chance of overflow so unchecked can be used to save gas. I ran a simple test in remix and found deployment savings of ~31,653 gas and on each function call saved ~141 gas per iteration.

**LOC:**
[EntryPoint.sol#L100](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L100) 
[EntryPoint.sol#L107](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L107) 
[EntryPoint.sol#L112](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L112) 
[EntryPoint.sol#L128](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L128) 
[EntryPoint.sol#L134](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol#L134) 

**Gas Savings Test:**
``` 
contract Test { 
	function loopTest() external { 
		for (uint256 i; i < 1; ++i) { 
			Deployment Cost: 125,637, Cost on function call: 24,601 
		
		vs 
		
		for (uint256 i; i < 1; ) { 
			// for loop body 
			unchecked { ++i } 
			Deployment Cost: 93,984, Cost on function call: 24,460 
		} 
	} 
} 
```


#### [G02] x = x + y is Cheaper than x += y
**Description:** 
When incrementing a state variables current value by y, you can save a small amount of gas by using the pattern x = x +/- y instead of x +/-= y. Based on a test in remix you can save ~1,007 gas on deployment and ~15 gas on each execution.

**LOC:**
[VerifyingSingletonPaymaster.sol#L51](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L51) 
[VerifyingSingletonPaymaster.sol#L58](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L58) 
[VerifyingSingletonPaymaster.sol#L128](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L128) 

**Gas Savings Test:**
``` 
contract Test { 
	uint256 x = 1; 
	function test() external { 
		x += 3; 
		(Deployment Cost: 153,124, Execution Cost: 30,369) 
		
		vs 
		
		x = x + 1; 
		(Deployment Cost: 152,117, Execution Cost: 30,354) 
	} 
} 
```


#### [G03] && in require statements
**Description:** 
If optimising for runtime costs over deployment costs you can seperate && in require functions into 2 parts. I ran a basic test in remix and it cost an extra 234 gas to deploy but will save ~9 gas everytime the require function is called. (note: If you upgrade to solidity version > 0.8.13 this is no longer true) 

**LOC:**
[ModuleManager.sol#L34](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L34) 
[ModuleManager.sol#L49](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L49) 
[ModuleManager.sol#L68](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L68) 

**Gas Savings Test:**
```  solidity 
contract Test { 
	uint256 a = 0; 
	uint256 b = 1; 
	function test() external { 
		require(a == 0 && b > a) 
		(Deployment cost: 123,291, Cost on function call: 29,371) 
		
		vs 
		
		require(a == 0); 
		require(b > a); 
		(Deployment cost: 123,525, Cost on function call: 29,362) 
	} 
} 
```


#### [G04] Deleting Mappings is Cheaper than setting to Default Value
**Description:** 
Based on this test in remix you can save ~511 gas in deployment costs and ~6 gas on each function call by using delete instead of setting a mapping to the default value. 

**LOC:**
[ModuleManager.sol#L52](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/base/ModuleManager.sol#L52) 

**Gas Savings Test:**
``` solidity 
contract Test { 
	mapping (address => uint256) public withdrawals; 
	function test(address a) external { 
		withdrawals[a] = 0; 
		(Deployment cost: 180,368, Execution cost: 27,820) 
		
		vs 
		
		delete withdrawals[a]; 
		(Deployment cost: 179,857, Execution cost: 27,814) 
	} 
} 
```


#### [G05] Referencing State variables more than once.
**Description:**
Whenever referencing a state variable more than once in a function without modifying it, you can save ~100 gas per use by caching the value and referencing that instead. (Note: Based on a test in remix deployment costs will be slightly higher (~759 gas).

**LOC:**
[StakeManager.sol#L82-L84](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L82-L84) - can cache info.unstakeDelaySec.
[StakeManager.sol#L100-L101](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L100-L101) - can cache info.withdrawTime.
[StakeManager.sol#L117-L118](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/StakeManager.sol#L117-L118) - can cache info.deposit.

**Gas Savings Test:**
```
contract Test {
	uint256 testVariable = 4;

	function testFunction() public {
		require(testVariable < 10);
		require(testVariable > 1);
		(Deployment cost: 120,933, Execution cost: 26,947)

		vs

		uint256 testCache = testVariable;
		require(testCache < 10);
		require(testCache > 1);
		(Deployment cost: 121,692, Execution cost: 26,847) 
	}
}
```