## [L-01] UNRESOLVED TODO AND QUESTION COMMENTS
Comments regarding todos and unanswered questions indicate that there are unresolved action items for implementation, which need to be addressed before the protocol deployment. Please review the todo and question comments in the following code.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L266
```solidity
        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L22-L26
```solidity
        // max supply == 0 means mint at will. 
        // initialSupply_ == 0 means nothing preminted
        // Therefore, we have valid scenarios if either of them is 0
        // However, if both are 0 we might have a valid scenario as well - user just wants to create a token but doesn't want to mint anything
        // Should we allow this?
```

## [N-01] `tokenAddress` CAN BE RENAMED
In the following `constructor`, `_tokenAddress` and `tokenAddress` are not exactly the token's address. To be more accurate and to avoid confusion, `tokenAddress` can be renamed to something like `token`.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L81-L84
```solidity
    constructor(IERC20 _tokenAddress) {
        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
        tokenAddress = _tokenAddress;
    }
```

## [N-02] INCORRECT AND REDUNDANT COMMENT
The second comment for `uint112 range` in the following code is redundant and also incorrect. To avoid confusion, it can be removed.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L40-L41
```solidity
        // uint112 range: range 0 –     5,192,296,858,534,827,628,530,496,329,220,095.
        // uint112 range: range 0 –                             5,192,296,858,534,827.
```

## [N-03] COMMENTED OUT CODE CAN BE REMOVED
The following code is commented out. To improve readability and maintainability, please consider removing it.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L9
```solidity
    // uint constant _initialSupply = 100 * (10**18);
```

## [N-04] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.
```solidity
contracts\AccessProtected.sol
  29: function isAdmin(address _addressToCheck) external view returns (bool) {

contracts\token\VariableSupplyERC20Token.sol
  36: function mint(address account, uint256 amount) public onlyAdmin {
```

## [N-05] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. @param or @return comments are missing for the following functions. Please consider completing NatSpec comments for them.
```solidity
contracts\VTVLVesting.sol
  91: function getClaim(address _recipient) external view returns (Claim memory) {    
  147: function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {  
  196: function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {  
  206: function finalVestedAmount(address _recipient) public view returns (uint112) {  
  215: function claimableAmount(address _recipient) external view returns (uint112) {  
  223: function allVestingRecipients() external view returns (address[] memory) {  
  230: function numVestingRecipients() external view returns (uint256) {  
  333: function createClaimsBatch( 
```

## [N-06] FLOATING PRAGMAS
It is a best practice to lock pragmas instead of using floating pragmas to ensure that contracts are tested and deployed with the intended compiler version. Accidentally deploying contracts with different compiler versions can lead to unexpected risks and undiscovered bugs. Please consider locking pragma for the `VariableSupplyERC20Token` contract.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2
```solidity
pragma solidity ^0.8.14;
```