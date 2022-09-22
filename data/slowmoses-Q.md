## (1) Solidity Version

All source files specify solidity version 0.8.14 EXCEPT for VariableSupplyERC20Token.sol which specifies ^0.8.14. Is there a reason for this?

Consider using the same version of solidity in all files.

Also consider using the most recent version of solidity.
0.8.17 is already released.

## (2) There is an Open Todo

// Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L266

## (3) Missing Indexed Fields in Event

Each event should use three indexed fields if there are three or more fields in the event.

***

event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L69

## (4) There is an Open Question in a Comment

// Therefore, we have valid scenarios if either of them is 0
// However, if both are 0 we might have a valid scenario as well - user just wants to create a token but doesn't want to mint anything
// Should we allow this?
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L24-L27

I would say, the answer to above question is “yes” and the code line therefore is erroneous. Either way, the question should be somehow resolved.
