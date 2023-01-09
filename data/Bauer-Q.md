### Use Two-Step Transfer Pattern for Access Controls

 # Description：
Contracts implementing access control's, e.g. owner, implementation ，EntryPoint should consider implementing a Two-Step Transfer pattern.
Otherwise it's possible that the role mistakenly transfers ownership or updates implementation  or updates EntryPoint  to the wrong address, resulting in a loss of the role.

function setOwner(address _newOwner) external mixedAuth {
        require(_newOwner != address(0), "Smart Account:: new Signatory address cannot be zero");
        address oldOwner = owner;
        owner = _newOwner;
        emit EOAChanged(address(this), oldOwner, _newOwner);
    }

    /**
     * @notice Updates the implementation of the base wallet
     * @param _implementation New wallet implementation
     */
    function updateImplementation(address _implementation) external mixedAuth {
        require(_implementation.isContract(), "INVALID_IMPLEMENTATION");
        _setImplementation(_implementation);
        // EOA + Version tracking
        emit ImplementationUpdated(address(this), VERSION, _implementation);
    }

    function updateEntryPoint(address _newEntryPoint) external mixedAuth {
        require(_newEntryPoint != address(0), "Smart Account:: new entry point address cannot be zero");
        emit EntryPointChanged(address(_entryPoint), _newEntryPoint);
        _entryPoint = IEntryPoint(payable(_newEntryPoint));
    }



# Recommended Mitigation Steps：


function setPendingOwner(address newPendingOwner) external {
    require(msg.sender == owner, "!owner");
    emit NewPendingOwner(newPendingOwner);
    pendingOwner = newPendingOwner;
}


function acceptOwnership() external {
    require(msg.sender == pendingOwner, "!pendingOwner");
    emit NewOwner(pendingOwner);
    owner = pendingOwner;
    pendingOwner = address(0);
}