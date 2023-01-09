### [G-1] Use function instead of modifiers

replace modifier with private view function

EG
``` solidity
function mixedAuth () private view {
80:         require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
82:    }

SmartAccount.sol
106:     function setOwner(address _newOwner) external (audit --deleted mixedAuth ){
++ add replace mixedAuth();
}


```


``` solidity



contracts\SmartAccount.sol
79:     modifier mixedAuth {
80:         require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
81:         _;
82:    }
File: c:\Users\pc\Desktop\code\hardhat\contracts\SmartAccount.sol
79:     modifier mixedAuth {
80:         require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
81:         _;
82:    }


contracts\SmartAccountNoAuth.sol
76:     modifier onlyOwner {
77:         require(msg.sender == owner, "Smart Account:: Sender is not authorized");
78:         _;
79:     }
80: 
81:     // onlyOwner OR self
82:     modifier mixedAuth {
83:         require(msg.sender == owner || msg.sender == address(this),"Only owner or self");
84:         _;
85:    }
86: 
87:    // only from EntryPoint
88:    modifier onlyEntryPoint {
89:         require(msg.sender == address(entryPoint()), "wallet: not from EntryPoint");
90:         _; 
91:    }

SimpleAccount.sol
47:     modifier onlyOwner() {
48:         _onlyOwner();
49:         _;
50:     }

SelfAuthorized.sol
10:     modifier authorized() {
11:         // This is a function call as it minimized the bytecode size
12:         requireSelfCall();
13:         _;
14:     }


```
