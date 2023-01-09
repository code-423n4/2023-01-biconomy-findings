(1) Gas Optimization, SmartAccount.sol, Line 468-474

Current for loop: 
       for (uint i = 0; i < dest.length;) {
            _call(dest[i], 0, func[i]);
            unchecked {
                ++i;
            }


Optimized loop:
for (uint i = 0; i < dest.length; i++) {
    _call(dest[i], 0, func[i]);
    unchecked
}

(2) Gas opt, SmartAccount.sol, Line 110, require String is too long for a 32 byte String literal. 
Current: require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");

Fixed:
require(_newOwner != address(0), "Signatory addr can't be 0");

(3) Gas opt, SmartAccount.sol, Line 128, require String is too long for a 32 byte String literal. 
Current: require(_newEntryPoint != address(0), "Smart Acct:: new entry point address cannot be zero");

Fixed:

Current: require(_newEntryPoint != address(0), "new entry point addr can't be 0");