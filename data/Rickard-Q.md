# USE A MORE RECENT VERSION OF SOLIDITY
Use a solidity version of at least 0.8.2 to get simple compiler automatic inclining.
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads.
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper than revert() / require().
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the call has a return value.
Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)
Use at least 0.8.13 to adopt using for with a list of free functions. 

- BaseSmartAccount.sol
- Proxy.sol
- SmartAccount.sol
- SmartAccountFactory.sol
- IAggregator.sol
- Executor.sol
- FallbackManager.sol
- ModuleManager.sol
- Enum.sol
- SecuredTokenTransfer.sol
- SignatureDecoder.sol
- Singleton.sol
- DefaultCallbackHandler.sol
- ERC721TokenReceiver.sol
- ERC777TokensRecipient.sol
- ERC1155TokenReceiver.sol
- IERC165.sol
- IERC1271Wallet.sol
- ISignatureValidator.sol
- LibAddress.sol
- Math.sol
- MultiSend.sol
- MultiSendCallOnly.sol

There are 23 instances of this issue:
```
pragma solidity 0.8.12;
```
# USE CUSTOM IMPORTS INSTEAD OF THE PLAIN “IMPORT” “FILE.SOL”
There are 38 instances of this issue:
BaseSmartAccount.sol
```
10	-	import "./common/Enum.sol";
10	+	import {Enum} from "./common/Enum.sol";
```
SmartAccount.sol
```
4	-	import "./libs/LibAddress.sol";
8	-	import "./BaseSmartAccount.sol";
9	-	import "./common/Singleton.sol"; 
10	-	import "./base/ModuleManager.sol"; 
11	-	import "./base/FallbackManager.sol"; 
12	-	import "./common/SignatureDecoder.sol"; 
13	-	import "./common/SecuredTokenTransfer.sol";
14	-	import "./interfaces/ISignatureValidator.sol";
15	-	import "./interfaces/IERC165.sol";

4	+	import {LibAddress} from "./libs/LibAddress.sol";
8	+	import {BaseSmartAccount} from "./BaseSmartAccount.sol";
9	+	import {Singleton} from "./common/Singleton.sol"; 
10	+	import {ModuleManager} from "./base/ModuleManager.sol"; 
11	+	import {FallbackManager} from "./base/FallbackManager.sol"; 
12	+	import {SignatureDecoder} from "./common/SignatureDecoder.sol"; 
13	+	import {SecuredTokenTransfer} from "./common/SecuredTokenTransfer.sol";
14	+	import {ISignatureValidator} from "./interfaces/ISignatureValidator.sol";
15	+	import {IERC165} from "./interfaces/IERC165.sol";
```
SmartAccountFactory.sol
```
4	-	import "./Proxy.sol"; 
5	-	import "./BaseSmartAccount.sol";

4	+	import {Proxy} from "./Proxy.sol"; 
5	+	import {BaseSmartAccount} "./BaseSmartAccount.sol";
```
```
12	-	import "../interfaces/IAccount.sol";
13	-	import "../interfaces/IPaymaster.sol"; 
14	
15	-	import "../interfaces/IAggregatedAccount.sol"; 
16	-	import "../interfaces/IEntryPoint.sol";
17	-	import "../utils/Exec.sol";
18	-	import "./StakeManager.sol"; 
19	-	import "./SenderCreator.sol"; 

12	+	import {IAccount} from "../interfaces/IAccount.sol";
13	+	import {IPaymaster} from "../interfaces/IPaymaster.sol"; 
14	
15	+	import {IAggregatedAccount} from "../interfaces/IAggregatedAccount.sol"; 
16	+	import {IEntryPoint} from "../interfaces/IEntryPoint.sol";
17	+	import {Exec} from "../utils/Exec.sol";
18	+	import {StakeManager} from "./StakeManager.sol"; 
19	+	import {SenderCreator} from "./SenderCreator.sol"; 
```
StakeManager.sol
```
5	-	import "../interfaces/IStakeManager.sol";
5	+	import {IStakeManager} from "../interfaces/IStakeManager.sol";
```
IAccount.sol
```
5	-	import "./UserOperation.sol";
5	+	import {UserOperation} from "./UserOperation.sol";

```
IAccount.sol
```
5	-	import "./UserOperation.sol";
5	+	import {UserOperation} from "./UserOperation.sol";

```
IAggregatedAccount.sol
```
5	-	import "./UserOperation.sol";
6	-	import "./IAccount.sol";
7	-	import "./IAggregator.sol";

5	+	import {UserOperation} from "./UserOperation.sol";
6	+	import {IAccount} from "./IAccount.sol";
7	+	import {IAggregator} from "./IAggregator.sol";
```
IAggregator.sol
```
5	-	import "./UserOperation.sol";
5	+	import {UserOperation} from "./UserOperation.sol";
```
IEntryPoint.sol
```
13	-	import "./UserOperation.sol";
14	-	import "./IStakeManager.sol";
15	-	import "./IAggregator.sol";

13	+	import {UserOperation} from "./UserOperation.sol";
14	+	import {IStakeManager} from "./IStakeManager.sol";
15	+	import {IAggregator} from "./IAggregator.sol";
```
IPaymaster.sol
```
5	-	import "./UserOperation.sol";
5	+	import {UserOperation} from "./UserOperation.sol";
```
Executor.sol
```
4	-	import "../common/Enum.sol";
4	+	import {Enum} from "../common/Enum.sol";

```
FallbackManager.sol
```
4	-	import "../common/SelfAuthorized.sol";
4	+	import {SelfAuthorized} from "../common/SelfAuthorized.sol";
```
ModuleManager.sol
```
4	-	import "../common/Enum.sol";
5	-	import "../common/SelfAuthorized.sol";
6	-	import "./Executor.sol";

4	+	import {Enum} from "../common/Enum.sol";
5	+	import {SelfAuthorized} from "../common/SelfAuthorized.sol";
6	+	import {Executor} from "./Executor.sol";
```
DefaultCallbackHandler.sol
```
4	-	import "../interfaces/ERC1155TokenReceiver.sol";
5	-	import "../interfaces/ERC721TokenReceiver.sol";
6	-	import "../interfaces/ERC777TokensRecipient.sol";
7	-	import "../interfaces/IERC165.sol";

4	-	import {ERC1155TokenReceiver} from "../interfaces/ERC1155TokenReceiver.sol";
5	-	import {ERC721TokenReceiver} from "../interfaces/ERC721TokenReceiver.sol";
6	-	import {ERC777TokensRecipient} from "../interfaces/ERC777TokensRecipient.sol";
7	-	import {IERC165} from "../interfaces/IERC165.sol";
```
# SOLIDITY COMPILER OPTIMIZATIONS CAN BE PROBLEMATIC
- [hardhat.config.ts#L35](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/hardhat.config.ts#L35)
- [hardhat.config.ts#L41](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/hardhat.config.ts#L41)
- [hardhat.config.ts#L47](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/hardhat.config.ts#L47)
```
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
**Recommended Mitigation Steps**
Short term: measure gas savings and weigh them against bug possibility.
Long term: monitor solidity compiler optimization updates and its maturity.

# MULTIPLE FUNCTIONS WITH THE SAME NAME MAY BE CONFUSING
IER1271.sol: [16](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol#L16), [32](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/interfaces/IERC1271Wallet.sol#L32)
```
16	  function isValidSignature(
32	  function isValidSignature(
```
SmartAccount.sol: [93](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L93), [97](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L97)
```
93	   function nonce() public view virtual override returns (uint256) {
97	   function nonce(uint256 _batchId) public view virtual override returns (uint256) {
```
**Recommended Mitigation Steps**
Consider changing one of the two functions, in that way it will make things clearer for user and dev.
# MISSING NATSPEC
[ERC777TokensRecipient.sol](https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/interfaces/ERC777TokensRecipient.sol)
```
// SPDX-License-Identifier: LGPL-3.0-only
pragma solidity 0.8.12;
interface ERC777TokensRecipient {
    function tokensReceived(
        address operator,
        address from,
        address to,
        uint256 amount,
        bytes calldata data,
        bytes calldata operatorData
    ) external;
}
```
# NOT NAMING THE PARAMETER IN THE FUNCTION IS CONFUSING
- [DefaultCallbackHandler.sol#L15-L23](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L15-L23)
- [DefaultCallbackHandler.sol#L25-L33](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L25-L33)
- [DefaultCallbackHandler.sol#L35-L42](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L35-L42)
- [DefaultCallbackHandler.sol#L44-L53](https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/handler/DefaultCallbackHandler.sol#L44-L53)
```
15	    function onERC1155Received(
16	        address,
17	        address,
18	        uint256,
19	        uint256,
 20	       bytes calldata
21	    ) external pure override returns (bytes4) {
22	        return 0xf23a6e61;
23	    }
24	
25	    function onERC1155BatchReceived(
26	        address,
27	        address,
28	        uint256[] calldata,
29	        uint256[] calldata,
30	        bytes calldata
31	    ) external pure override returns (bytes4) {
32	        return 0xbc197c81;
33	    }
34	
35	    function onERC721Received(
36	        address,
37	        address,
38	        uint256,
39	       bytes calldata
40	    ) external pure override returns (bytes4) {
41	        return 0x150b7a02;
42	    }
43	
44	    function tokensReceived(
45	        address,
46	        address,
47	        address,
48	        uint256,
49	        bytes calldata,
50	        bytes calldata
51	    ) external pure override {
52	        // We implement this for completeness, doesn't really have any value
53	    }
```
