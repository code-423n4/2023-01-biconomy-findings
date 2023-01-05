https://github.com/code-423n4/2023-01-biconomy/blob/53c8c3823175aeb26dee5529eeefa81240a406ba/scw-contracts/contracts/smart-contract-wallet/SmartAccount.sol#L166

function init.

1.1 line 174: "if" isnt need,because the same condition there is in require on line 171
1.2 line 167-168: no need requires, because function has modifier initializer

it reduce gas of function SmartAccountFactory.sol -> deployCounterFactualWallet.

before: min: 261088, max: 261124
after: min: 260796, max: 260832

and reduce gas of function SmartAccountFactory.sol -> deployWallet