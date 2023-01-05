# NON CRITICAL FINDINGS
---------------------------------------------------------------------------------------------------------------------------------------------------------------------

##

### [NC-1] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol)

     2:  pragma solidity ^0.8.12;

##

### [NC-2] USE A MORE RECENT VERSION OF SOLIDITY

Latest Solidity version is 0.8.17. Lots of new features there in latest solidity versions 

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol)

       2: pragma solidity 0.8.12;

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/Proxy.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/Proxy.sol)

       2: pragma solidity 0.8.12;

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

      2: pragma solidity 0.8.12;

[FILE : 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)

      6:  pragma solidity ^0.8.12;

##

### [NC-3]  Shorter the inheritance list

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

        contract SmartAccount is 
        Singleton,
        BaseSmartAccount,
        IERC165,
        ModuleManager,
        SignatureDecoder,
        SecuredTokenTransfer,
        ISignatureValidatorConstants,
        FallbackManager,
        Initializable,
        ReentrancyGuardUpgradeable {

##

## [NC-4]  NATSPEC IS MISSING

Description
NatSpec is missing for the following functions , constructor and modifier

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)  

         function handlePayment(
        uint256 gasUsed,
        uint256 baseGas,
        uint256 gasPrice,
        uint256 tokenGasPriceFactor,
        address gasToken,
        address payable refundReceiver
       ) private nonReentrant returns (uint256 payment) {


        function handlePaymentRevert(
        uint256 gasUsed,
        uint256 baseGas,
        uint256 gasPrice,
        uint256 tokenGasPriceFactor,
        address gasToken,
        address payable refundReceiver
    ) external returns (uint256 payment) {



# LOW FINDINGS

---------------------------------------------------------------------------------------------------------------------------------------------------------------

##

### [L-1]  USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/interfaces/IAccount.sol)

          4:  import "./UserOperation.sol";

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/BaseSmartAccount.sol)

          8:   import "@account-abstraction/contracts/interfaces/IAccount.sol";

          9:    import "@account-abstraction/contracts/interfaces/IEntryPoint.sol";

         10:  import "./common/Enum.sol";

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)

       4:  import "./libs/LibAddress.sol";

       5:  import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

       6:  import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

       7:   import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";

       8:  import "./BaseSmartAccount.sol";

       9:  import "./common/Singleton.sol";

       10:   import "./base/ModuleManager.sol";

       11:  import "./base/FallbackManager.sol";

       12:   import "./common/SignatureDecoder.sol";

       13:  import "./common/SecuredTokenTransfer.sol";

       14:  import "./interfaces/ISignatureValidator.sol";

       15:  import "./interfaces/IERC165.sol";

       16:  import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

[FILE : 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/aa-4337/core/EntryPoint.sol)

          import "../interfaces/IAccount.sol";
          import "../interfaces/IPaymaster.sol";
          import "../interfaces/IAggregatedAccount.sol";
         import "../interfaces/IEntryPoint.sol";
         import "../utils/Exec.sol";
         import "./StakeManager.sol";
         import "./SenderCreator.sol";

##

## [L-2]  CRITICAL ADDRESS CHANGES SHOULD USE TWO-STEP PROCEDURE  

> The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
(https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure)


[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)  


        function setOwner(address _newOwner) external mixedAuth {
        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
        address oldOwner = owner;
        owner = _newOwner;
        emit EOAChanged(address(this), oldOwner, _newOwner);
         }


        function updateEntryPoint(address _newEntryPoint) external mixedAuth {
        require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
        emit EntryPointChanged(address(_entryPoint), _newEntryPoint);
        _entryPoint = IEntryPoint(payable(_newEntryPoint));
        }

Recommended Mitigation Steps

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

##

## [L-3]  UNUSED RECEIVE() FUNCTION WILL LOCK ETHER IN CONTRACT

    If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)  

       550:  receive() external payable {}


> Recommended Mitigation Steps

The function should call another function, otherwise it should revert

##

## [L-4]  _newEntryPoint not checked its already a entry point or not  . Its possible to set already exist address as new entry point . The new entry point must not same with already existing address . As per current protocol its possible to set already exiting address as a new entry.

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)  

        function updateEntryPoint(address _newEntryPoint) external mixedAuth {
        require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
        emit EntryPointChanged(address(_entryPoint), _newEntryPoint);
        _entryPoint = IEntryPoint(payable(_newEntryPoint));
        }

> Recommended Mitigation Steps

 require(_newEntryPoint != _entryPoint , "New entry point address is already a entry point"); . Need to add this in the updateEntryPoint() code.

After Mitigation :

        function updateEntryPoint(address _newEntryPoint) external mixedAuth {
        require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
        require(_newEntryPoint != _entryPoint , "Smart Account:: New entry point address is already a entry point");
        emit EntryPointChanged(address(_entryPoint), _newEntryPoint);
        _entryPoint = IEntryPoint(payable(_newEntryPoint));
        }

##

## [L-5]  _newOwner address must be checked with existing owner address . Its possible to set already existing owner address as new owner.

[FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)  

        function setOwner(address _newOwner) external mixedAuth {
        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
        address oldOwner = owner;
        owner = _newOwner;
        emit EOAChanged(address(this), oldOwner, _newOwner);
       }

> Recommended Mitigation Steps

require(_newOwner != owner , "New owner address already owner");

Add require statement to __newOwner address already exiting owner

        function setOwner(address _newOwner) external mixedAuth {
        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
        require(_newOwner != owner , "New owner address already owner");
        address oldOwner = owner;
        owner = _newOwner;
        emit EOAChanged(address(this), oldOwner, _newOwner);
       }

##

## [L-6]  AVOID HARDCODED VALUES

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code

           [FILE: 2023-01-biconomy/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol)  

             200:   uint256 startGas = gasleft() + 21000 + msg.data.length * 8;

            42:     bytes32 internal constant DOMAIN_SEPARATOR_TYPEHASH = 0x47e79534a245952e8b16893a336b85a3d9ea9fa8c573f3d803afb92a79469218;


           48:    bytes32 internal constant ACCOUNT_TX_TYPEHASH = 0xc2595443c361a1f264c73470b9410fd67ac953ebd1a3ae63a2f514f3f014cf07;

           224 :  require(gasleft() >= max((_tx.targetTxGas * 64) / 63,_tx.targetTxGas + 2500) + 500, "BSA010");
         








NC-1	require() / revert() statements should have descriptive reason strings	3
NC-2	Event is missing indexed fields	9
NC-3	Constants should be defined rather than using magic numbers	1
NC-4	Functions not used internally could be marked external	17

L-1	abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()	4
L-2	Unspecific compiler version pragma	1

