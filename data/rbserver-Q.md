## [L-01] FOUNDERS' ADMIN RIGHTS CAN BE REVOKED BY OTHER ADMINS
Usually, the deployer is one of the founders. After the deployment, the deployer can call the following `setAdmin` function to grant admin rights to other individual, who may not be one of the founders. This requires huge trust between the founders and added admins because these added admins can also call `setAdmin` to revoke the admin rights from the founders. In case if the organization would want to protect the founders, especially if the founders are the ones who provide fundings or attract fundings, please consider creating a founder role and make `setAdmin` callable only by the addresses with this founder role.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39-L43
```solidity
    function setAdmin(address admin, bool isEnabled) public onlyAdmin {
        require(admin != address(0), "INVALID_ADDRESS");
        _admins[admin] = isEnabled;
        emit AdminAccessSet(admin, isEnabled);
    }
```

## [L-02] `linearVestAmount` IS NOT REQUIRED TO BE SET BUT `startTimestamp` IS
When calling the following `_createClaimUnchecked` function, it is possible to create a claim with 0 `linearVestAmount`, which is also confirmed by the [documentation](https://github.com/code-423n4/2022-09-vtvl#claim) that states: "Each of the parts (cliff and linear) have amounts that can be allocated to each. The founders can opt to use either or both options for each of the claims." However, `startTimestamp` is required to be set for the claim due to `require(_startTimestamp > 0, "INVALID_START_TIMESTAMP")`, which is somewhat inconsistent. If this is intended for the design, then this requirement needs to be clearly communicated with the users of the protocol. Otherwise, the protocol can allow claims to be created without setting `startTimestamp` and update the implementations, such as the following `hasActiveClaim` modifier, to not depend on claim's `startTimestamp` in some occasions.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L245-L304
```solidity
    function _createClaimUnchecked(
            address _recipient, 
            uint40 _startTimestamp, 
            uint40 _endTimestamp, 
            uint40 _cliffReleaseTimestamp, 
            uint40 _releaseIntervalSecs, 
            uint112 _linearVestAmount, 
            uint112 _cliffAmount
                ) private  hasNoClaim(_recipient) {

        require(_recipient != address(0), "INVALID_ADDRESS");
        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

        ...

	}
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L105-L117
```solidity
    modifier hasActiveClaim(address _recipient) {
        Claim storage _claim = claims[_recipient];
        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

        // We however still need the active check, since (due to the name of the function)
        // we want to only allow active claims
        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

        // Save gas, omit further checks
        // require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
        // require(_claim.endTimestamp > 0, "NO_END_TIMESTAMP");
        _;
    }
```

## [L-03] UNRESOLVED TODO AND QUESTION COMMENTS
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