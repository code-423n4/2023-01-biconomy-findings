Check that a new EntryPoint being set, is a Contract address

SmartAccount.sol

Currently those that have authorisation / access to the updateEntryPoint() method can pass in an EOA, which would make the EntryPoint interface inaccessible if the EntryPoint address is changed to an EOA. Also it would allow for arbitrary execution of the execFromEntryPoint() method on the SmartAccount contract, which may not be warranted from the owner of the contract, as this allows access to functions of the SCW methods.

        require(_newEntryPoint.isContract(), "INVALID_ENTRY_POINT");
