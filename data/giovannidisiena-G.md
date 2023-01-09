### **Gas Optimisations**
#### **[G-01] Variable Assignment in Event**
5 gas can be saved by assigning `owner` within the event. This also follows checks-effects-interactions due to right-to-left evaluation.

*There is 1 instance of this issue:*

```
File: contracts/smart-contract-wallet/SmartAccount.sol

    function setOwner(address _newOwner) external mixedAuth {
        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
        address oldOwner = owner;
        owner = _newOwner;
        emit EOAChanged(address(this), oldOwner, _newOwner);
    }
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L109-L114

#### **[G-02] Use Global Address Code Length**
10 gas can be saved by using the built-in `<address>.code.length > 0`.

*There are 2 instances of this issue:*

```
File: contracts/smart-contract-wallet/libs/LibAddress.sol

    /**
     * @notice Will return true if provided address is a contract
     * @param account Address to verify if contract or not
     * @dev This contract will return false if called within the constructor of
     *      a contract's deployment, as the code is not yet stored on-chain.
     */
    function isContract(address account) internal view returns (bool) {
        uint256 csize;
        // solhint-disable-next-line no-inline-assembly
        assembly { csize := extcodesize(account) }
        return csize != 0;
    }
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/LibAddress.sol#LL5C3-L16C4

```
File: contracts/smart-contract-wallet/paymasters/verifying/VerifyingSingletonPaymaster.sol

    import "@openzeppelin/contracts/utils/Address.sol";
    ...
    require(!Address.isContract(paymasterId), "Paymaster Id can not be smart contract address");
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L49

#### **[G-03] Use Global Block Chain Id**
13 gas can be saved by using the built-in `block.chainid` in place of this function. This also reduces the bytecode size as this function can be removed completely, given it is only used once.

*There is 1 instance of this issue:*

```
File: contracts/smart-contract-wallet/SmartAccount.sol

    /// @dev Returns the chain id used by this contract.
    function getChainId() public view returns (uint256) {
        uint256 id;
        // solhint-disable-next-line no-inline-assembly
        assembly {
            id := chainid()
        }
        return id;
    }
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L139-L147

#### **[G-04] Simplify Signature Checking Arithmetic**
115 can be saved by removing multiplication by 1 in signature checking arithmetic given the account is a 1/1 multisig and so the number of required signatures will only ever be 1. This will be true unless the code changes to support multiple signers per account, in which case it is recommended to document this saving and code requirement for the case of multiple signers. It is also recommended to remove the unnecessary stack variable `i` on #L302 and pass `0` to `signatureSplit` directly.

*There is 1 instance of this issue:*

```
File: contracts/smart-contract-wallet/SmartAccount.sol

    /**
     * @dev Checks whether the signature provided is valid for the provided data, hash. Will revert otherwise.
     * @param dataHash Hash of the data (could be either a message hash or transaction hash)
     * @param signatures Signature data that should be verified. Can be ECDSA signature, contract signature (EIP-1271) or approved hash.
     */
    function checkSignatures(
        bytes32 dataHash,
        bytes memory data,
        bytes memory signatures
    ) public view virtual {
        uint8 v;
        bytes32 r;
        bytes32 s;
        uint256 i = 0;
        address _signer;
        (v, r, s) = signatureSplit(signatures, i);
        //review
        if(v == 0) {
            // If v is 0 then it is a contract signature
            // When handling contract signatures the address of the contract is encoded into r
            _signer = address(uint160(uint256(r)));

                // Check that signature data pointer (s) is not pointing inside the static part of the signatures bytes
                // This check is not completely accurate, since it is possible that more signatures than the threshold are send.
                // Here we only check that the pointer is not pointing inside the part that is being processed
                require(uint256(s) >= uint256(1) * 65, "BSA021");
            ...
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L297-L322

#### **[G-05] Remove Unnecessary Local Variable**
Do not create local variables to cache memory reads when they are referenced only once and can be used directly.

*There are 2 instances of this issue:*

```
File: contracts/smart-contract-wallet/paymasters/verifying/VerifyingSingletonPaymaster.sol

    function withdrawTo(address payable withdrawAddress, uint256 amount) public override {
        uint256 currentBalance = paymasterIdBalances[msg.sender];
        require(amount <= currentBalance, "Insufficient amount to withdraw");
        paymasterIdBalances[msg.sender] -= amount;
        entryPoint.withdrawTo(withdrawAddress, amount);
    }
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/paymasters/verifying/singleton/VerifyingSingletonPaymaster.sol#L55-L60

```
File: contracts/smart-contract-wallet/SmartAccount.sol

    /**
     * @dev Checks whether the signature provided is valid for the provided data, hash. Will revert otherwise.
     * @param dataHash Hash of the data (could be either a message hash or transaction hash)
     * @param signatures Signature data that should be verified. Can be ECDSA signature, contract signature (EIP-1271) or approved hash.
     */
    function checkSignatures(
        bytes32 dataHash,
        bytes memory data,
        bytes memory signatures
    ) public view virtual {
        uint8 v;
        bytes32 r;
        bytes32 s;
        uint256 i = 0;
        address _signer;
        (v, r, s) = signatureSplit(signatures, i);
        ...
```
https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L302-L312

#### **[G-06] Remove Unnecessary Local Variable**
From a previous Gnosis Safe audit:
>1. Currently, the “transaction” parameter is a byte string that contains Call or DelegateCall
operations packed according to ABI. This leaves some gaps filled with zeros. If these gaps
were removed, the gas savings per operation could be around 400 gas. I understand that this
encoding is used because of client side simplicity (there are ready available functions to
perform ABI-style encoding of parameters).
2. Code inside the loop in the “multiSend” function could be improved by having “i” iterate over
actual memory pointers and not offsets relative to “transactions”. A few ADD instructions
would be spared and the code may become more readable.

*There is 1 instance of this issue:*

https://github.com/code-423n4/2023-01-biconomy/blob/main/scw-contracts/contracts/smart-contract-wallet/libs/MultiSend.sol