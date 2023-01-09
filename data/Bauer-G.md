# Long Revert Strings

## Description
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.
If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

instances(84):
BaseAccount.sol
49         require(msg.sender == address(entryPoint()), "account: not from EntryPoint");
EntryPoint.sol
170         require(msg.sender == address(this), "AA92 internal call only");
213            require(paymasterAndData.length >= 20, "AA93 invalid paymasterAndData");
264             if (sender.code.length != 0) revert FailedOp(opIndex, address(0), "AA10 sender already constructed");
266            if (sender1 == address(0)) revert FailedOp(opIndex, address(0), "AA13 initCode failed or OOG");
267              if (sender1 != sender) revert FailedOp(opIndex, address(0), "AA14 initCode must return sender");
268            if (sender1.code.length == 0) revert FailedOp(opIndex, address(0), "AA15 initCode must create sender");
301                revert FailedOp(0, address(0), "AA20 account not deployed");
305                revert FailedOp(0, address(0), "AA30 paymaster not deployed");
322                revert FailedOp(opIndex, address(0), "AA22 expired");
328            revert FailedOp(opIndex, address(0), "AA23 reverted (or OOG)");
334                revert FailedOp(opIndex, address(0), "AA21 didn't pay prefund");
353        require(verificationGasLimit > gasUsedByValidateAccountPrepayment, "AA41 too little verificationGas");
360            revert FailedOp(opIndex, paymaster, "AA31 paymaster deposit too low");
366                revert FailedOp(opIndex, paymaster, "AA32 paymaster expired");
373            revert FailedOp(opIndex, paymaster, "AA33 reverted (or OOG)");
397        require(maxGasValues <= type(uint120).max, "AA94 gas values overflow");
421             revert FailedOp(opIndex, mUserOp.paymaster, "AA40 over verificationGasLimit");
463                        revert FailedOp(opIndex, paymaster, "A50 postOp revert");
471            revert FailedOp(opIndex, paymaster, "A51 prefund below actualGasCost");

StakeManager.sol
41        require(newAmount <= type(uint112).max, "deposit overflow");
61        require(_unstakeDelaySec > 0, "must specify unstake delay");
63           require(unstakeDelaySec >= info.unstakeDelaySec, "cannot decrease unstake time");
        uint256 sstake > 0, "no stake specified");
65        require(stake < type(uint112).max, "stake overflow");
82        require(info.unstakeDelaySec != 0, "not staked");
83        require(info.staked, "already unstaking");
99        require(stake > 0, "No stake to withdraw");
100        require(info.withdrawTime > 0, "must call unlockStake() first");
101        require(info.withdrawTime <= block.timestamp, "Stake withdrawal is not due");
107        require(success, "failed to withdraw stake");
117        require(withdrawAmount <= info.deposit, "Withdraw amount too large");
121        require(success, "failed to withdraw");

MultiSend.sol
27        require(address(this) != multisendSingleton, "MultiSend should only be called via delegatecall");

VerifyingSingletonPaymaster.sol 
36        require(address(_entryPoint) != address(0), "VerifyingPaymaster: Entrypoint can not be zero address");
37        require(_verifyingSigner != address(0), "VerifyingPaymaster: signer of paymaster can not be zero address");
42        revert("Deposit must be for a paymasterId. Use depositFor");
49        require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");
50        require(paymasterId != address(0), "Paymaster Id can not be zero address");
57        require(amount <= currentBalance, "Insufficient amount to withdraw");
66        require(_newVerifyingSigner != address(0), "VerifyingPaymaster: new signer can not be zero address");
107        require(sigLength == 64 || sigLength == 65, "VerifyingPaymaster: invalid signature length in paymasterAndData");
108        require(verifyingSigner == hash.toEthSignedMessageHash().recover(paymasterData.signature), "VerifyingPaymaster: wrong signature");
109        require(requiredPreFund <= paymasterIdBalances[paymasterData.paymasterId], "Insufficient balance for paymaster id");

BaseSmartAccount.sol
74        require(msg.sender == address(entryPoint()), "account: not from EntryPoint");

SmartAccount.sol
77        require(msg.sender == owner, "Smart Account:: Sender is not authorized");
83        require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
89        require(msg.sender == address(entryPoint()), "wallet: not from EntryPoint");
110        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
121        require(_implementation.isContract(), "INVALID_IMPLEMENTATION");
128        require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
167        require(owner == address(0), "Already initialized");
168        require(address(_entryPoint) == address(0), "Already initialized");
169        require(_owner != address(0),"Invalid owner");
170        require(_entryPointAddress != address(0), "Invalid Entrypoint");
171        require(_handler != address(0), "Invalid Entrypoint");
348            require(_signer == owner, "INVALID_SIGNATURE");
351            require(_signer == owner, "INVALID_SIGNATURE");
458        require(dest != address(0), "this action will burn your funds");
452        require(success,"transfer failed");
467        require(dest.length == func.length, "wrong array lengths");
491        require(success, "Userop Failed");
495        require(msg.sender == address(entryPoint()) || msg.sender == owner, "account: not Owner or EntryPoint");
502        require(nonces[0]++ == userOp.nonce, "account: invalid nonce");
511        require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");

SmartAccountFactory.sol
18        require(_baseImpl != address(0), "base wallet address can not be zero");
40        require(address(proxy) != address(0), "Create2 call failed");

SmartAccountNoAuth.sol
77        require(msg.sender == owner, "Smart Account:: Sender is not authorized");
83        require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
89        require(msg.sender == address(entryPoint()), "wallet: not from EntryPoint");
110        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
121        require(_implementation.isContract(), "INVALID_IMPLEMENTATION");
128        require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
167        require(owner == address(0), "Already initialized");
168        require(address(_entryPoint) == address(0), "Already initialized");
169        require(_owner != address(0),"Invalid owner");
170        require(_entryPointAddress != address(0), "Invalid Entrypoint");
171        require(_handler != address(0), "Invalid Entrypoint");
343            require(_signer == owner || true, "INVALID_SIGNATURE");
346            require(_signer == owner || true, "INVALID_SIGNATURE");
440        require(dest != address(0), "this action will burn your funds");
442        require(success,"transfer failed");
457        require(dest.length == func.length, "wrong array lengths");
492        require(nonces[0]++ == userOp.nonce, "account: invalid nonce");
501        require(owner == hash.recover(userOp.signature) || tx.origin == address(0), "account: wrong signature");


# Recommended Mitigation Steps
Add code like this; require(condition, "LOK");