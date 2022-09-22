## Low risks
### L01:_SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
#### problem
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function
#### prof
token/FullPremintERC20Token.sol, 12, b'        _mint(_msgSender(), supply_);'
token/VariableSupplyERC20Token.sol, 45, b'        _mint(account, amount);'

### L02: INPUT ARRAY LENGTHS MAY DIFFER
#### problem
If the caller makes a copy-paste error, the lengths may be mismatchd and an operation believed to have been completed may not in fact have been completed
function withdrawMultipleERC721(address[] memory _tokens, uint256[] memory _tokenId, address _to) external override {
#### prof
VTVLVesting.sol, 358, b'    function createClaimsBatch(\n        address[] memory _recipients, \n        uint40[] memory _startTimestamps, \n        uint40[] memory _endTimestamps, \n        uint40[] memory _cliffReleaseTimestamps, \n        uint40[] memory _releaseIntervalsSecs, \n        uint112[] memory _linearVestAmounts, \n        uint112[] memory _cliffAmounts) \n        external onlyAdmin '

### L03: address variable should check if it is zero MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES
#### prof
VTVLVesting.sol, 83, b'        tokenAddress = _tokenAddress;'


### N01: inconsistent solidity version usage.
AccessProtected.sol, 2, b'pragma solidity 0.8.14;'
token/FullPremintERC20Token.sol, 2, b'pragma solidity 0.8.14;'
token/VariableSupplyERC20Token.sol, 2, b'pragma solidity ^0.8.14;'
VTVLVesting.sol, 2, b'pragma solidity 0.8.14;'

### N02: USE A MORE RECENT VERSION OF SOLIDITY
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value