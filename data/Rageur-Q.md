# QA report

## QA-01: Floating Pragma

### Description

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

• BasePaymaster.sol (Line: 2)
• EntryPoint.sol (Line: 6)
• Exec.sol (Line: 2)
• IAccount.sol (Line: 2)
• IAggregatedAccount.sol (Line: 2)
• IEntryPoint.sol (Line: 6)
• IPaymaster.sol (Line: 2)
• IStakeManager.sol (Line: 2)
• SenderCreator.sol (Line: 2)
• StakeManager.sol (Line: 2)
• UserOperation.sol (Line 2)