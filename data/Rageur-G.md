# Gas Report

## Gas-01: require()/revert() Strings Longer Than 32 Bytes Cost Extra Gas

### Description

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

• MultiSend.sol (Line: 27)
• SmartAccount.sol (Line: 77, 110, 128)
• SmartAccountFactory.sol (Line: 18, 77, 110, 128)
• VerifyingSingletonPaymaster.sol (Line: 36, 37, 42, 49, 50, 66, 107, 108, 109)


## Gas-02: <X> * 2 Pattern Cost More Gas Than <X> << 1

### Description

Using shifting instead of multiplication or division for values that are multiple of 2 [2, 4, 8, 16 ...] can reduce gas.

• Math.sol (Line: 36)
• SmartAccount.sol (Line: 200, 224)


## Gas-03: <X> += <Y> Pattern Cost More Gas Than <X> = <X> + <Y> For State Variable

### Description

Using the addition operator instead of plus-equals saves gas

• VerifyingSingletonPaymaster.sol (Line: 51, 58, 128)


## Gas-04: <X> <= <Y> Pattern Cost More Gas Than <X> < <Y> + 1

Using less or equal required more instruction than a single less hence reducing the gas

• EntryPoint.sol (Line: 213, 237, 397)
• SmartAccount.sol (Line: 182, 224, 322, 325, 333)
• SmartAccountNoAuth.sol (Line: 182, 224, 317, 320, 328)
• VerifyingSingletonPaymaster.sol (Line 57, 109)


## Gas-05: Missing unchecked{} For Subtractions Where The Operands Cannot Underflow Because Of A Previous require() or if Statement 

### Description

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }

This will stop the check for overflow and underflow so it will save gas

• EntryPoint.sol (Line 354)


## Gas-06: Missing unchecked{i++} When It's Not Possible To Overflow In For Loop

### Description

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
Moreover, to save even more gas, it is recommended to use ++i instead of i++.

• EntryPoint.sol (Line: 74, 80, 100, 107, 112, 128, 134)
• SimpleAccount.sol (Line: 71)


## Gas-07: Not Using The Named Return Variables When A Function Returns, Wastes Deployment Gas

### Description

The named return variable are not used which cause some extra gas at the deployment

• SmartAccount.sol (Line: 506)
• VerifyingSingletonPaymaster.sol (Line: 97)


##  Gas-08: require() Or revert() Statements That Check Input Arguments Should Be At Top Of The Function

### Description

Checks that involve constants should come before checks that involve state variables, function calls, and calculations.
By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

• ModuleManager.sol (Line 25)
• SmartAccount.sol (Line: 171)