-> EVENT IS MISSING INDEXED FIELDS
Each event should use three indexed fields if there are three or more fields.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#:~:text=event%20ClaimRevoked(address%20indexed%20_recipient%2C%20uint112%20_numTokensWithheld%2C%20uint256%20revocationTimestamp%2C%20Claim%20_claim)%3B

-> _SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE

_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#:~:text=_mint(_msgSender()%2C%20supply_)%3B
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#:~:text=%3E%200)%20%7B-,mint,-(_msgSender()%2C%20initialSupply_
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#:~:text=%7D-,_mint,-(account%2C%20amount)%3B


