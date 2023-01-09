## [L-01] Remove unused input/unnamed parameter 
File: VerifyingSingletonPaymaster, line: 97
- [VerifyingSingletonPaymaster.sol#L97](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L97)

The `/*userOpHash*/` is commented and not used.  Remove unused parameter.

```
function validatePaymasterUserOp(UserOperation calldata userOp, bytes32 /*userOpHash*/, uint256 requiredPreFund)
    external view override returns (bytes memory context, uint256 deadline) {
        (requiredPreFund);
        bytes32 hash = getHash(userOp);

        PaymasterData memory paymasterData = userOp.decodePaymasterData();
        uint256 sigLength = paymasterData.signatureLength;

        //ECDSA library supports both 64 and 65-byte long signatures.
        // we only "require" it here so that the revert reason on invalid signature will be of "VerifyingPaymaster", and not "ECDSA"
        require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
        require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");
        require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");
        return (userOp.paymasterContext(paymasterData), 0);
    }
```

### Recommended Mitigation Steps
- Remove the unused/commented `/*userOpHash*/`  parameter in `validatePaymasterUser` function.

## [L-02] Use latest stable pragma version 0.8.17 
Files: All files. 
Low level calls with solidity versions 0.8.14 and BELOW can result in optimizer bugs.
https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

see: https://swcregistry.io/docs/SWC-102
Simliar findings in Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#5-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug

### Recommended Mitigation steps
- Consider upgrading to latest Solidity pragma stable version 0.8.17

## [L-03] Use named import
files: All files
The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

Recommended Mitigation Steps
1. Used named imports like `import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol"` instead of `import @openzeppelin/contracts/token/ERC20/ERC20.sol`


## [L-04] Solidity compiler optimizations can be problematic
File: hardhat.config.js

```
const config: HardhatUserConfig = {
  // defaultNetwork: "ganache",
  solidity: {
    compilers: [
      {
        version: "0.8.12",
        settings: {
          optimizer: { enabled: true, runs: 200 },
        },
      },
      {
        version: "0.8.4",
        settings: {
          optimizer: { enabled: true, runs: 200 },
        },
      },
      {
        version: "0.8.9",
        settings: {
          optimizer: { enabled: true, runs: 200 },
        },
      },
    ],
  },

```
#### DESCRIPTION
Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

### Recommended Mitigation Steps

Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## [L-05] Use Latest Openzeppelin Library versions
The lastest version of Openzeppelin is 4.8.0. This project uses version 4.7.4

```
"dependencies": {
    "@account-abstraction/contracts": "^0.3.0",
    "@account-abstraction/sdk": "^0.3.0",
    "@chainlink/contracts": "^0.4.1",
    "@ethersproject/abstract-signer": "^5.6.2",
    "@ethersproject/constants": "^5.6.1",
    "@nomiclabs/hardhat-etherscan": "^2.1.6",
    "@openzeppelin/contracts": "^4.7.3",
    "@openzeppelin/contracts-upgradeable": "^4.7.3",
```
Recommended Mitigation Step
- Consider using the latest openzeppelin library.

## [L-06] Missing Event for critical parameter change
File: BasePaymaster.sol, line 24.

```
function setEntryPoint(IEntryPoint _entryPoint) public onlyOwner {
        entryPoint = _entryPoint;
    }
```

Recommended Mitigations steps
Emit events for critical state changes to allow frontend and monitoring tools  critical state changes.

## [L-07] Avoid Floating Pragma
Files: BaseAccount.sol, BasePaymaster.sol, SenderCreator.sol, StakeManager.sol
see: https://swcregistry.io/docs/SWC-103

### Recommended mitigation Step
Use locked Solidity pragma version for non library contracts.



## [NC-01] Remove commented code
File: SmartAccount.sol, line 201.

- [SmartAccount.sol#L201](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L201)
- SmartAccount.sol#L237](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L237)
- SmartAccount.sol#L243](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L243)
- SmartAccount.sol#L267](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L267)
- SmartAccount.sol#L268](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L268)
- SmartAccount.sol#L292](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L292)


## [NC-02] Missing NatSpec for some functions
Files: SmartAccount.sol, lines 127, 247, 271, 465 and 477


## [NC-03] Return Variable not added in some NatSpec
Files: SmartAccountFactory.sol, lines 68.



