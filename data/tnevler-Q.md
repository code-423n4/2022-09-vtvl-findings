# Report

## Low Risk ##

### [L-01]: Loops may exceed gas limit 

**Context:**
 
```
 for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

**Description:**

Either explicitly or just due to normal operation, the number of iterations in a loop can grow beyond the block gas limit, which can cause the complete contract to be stalled at a certain point.


## Non-Critical Issues ##

### [N-01]: Floating Pragma

**Context:**

```
pragma solidity ^0.8.14;
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2

**Recommendation:**

https://swcregistry.io/docs/SWC-103

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### [N-02]: Public function can be external
**Context:** 

+ [VariableSupplyERC20Token.mint](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36)
+ [AccessProtected.setAdmin](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39)
+ [VTVLVesting.withdrawAdmin](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398)

**Description:**

Public functions can be declared external if they are not called by the contract.

**Recommendation:**

Declare these functions as external instead of public.


### [N-03]: NatSpec is missing 
**Context:** 

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol

### [N-04]: NatSpec is incomplete

1. missing: '@notice', '@param account', '@param amount'
```
function mint(address account, uint256 amount) public onlyAdmin {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36

2. missing: '@notice', '@param _addressToCheck', '@return'
```
function isAdmin(address _addressToCheck) external view returns (bool) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L29

3. missing: '@param _recipient', '@param _claim'
```
     /**
    @notice Emitted when a founder adds a vesting schedule.
     */
    event ClaimCreated(address indexed _recipient, Claim _claim); 
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L56

4. missing: '@param _recipient', '@param _withdrawalAmount'
```
    /**
    @notice Emitted when someone withdraws a vested amount
    */
    event Claimed(address indexed _recipient, uint112 _withdrawalAmount); 
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L61

5. missing: '@param _recipient', '@param _numTokensWithheld', '@param revocationTimestamp', '@param_claim'
```
event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L66

6. missing: '@param _recipient', '@param _amountRequested'
```
    /** 
    @notice Emitted when a claim is revoked
    */
    event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L74

7. missing: '@param _tokenAddress'
```
    /**
    @notice Construct the contract, taking the ERC20 token to be vested as the parameter.
    @dev The owner can set the contract in question when creating the contract.
     */
    constructor(IERC20 _tokenAddress) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L77

8. missing: '@return'
```
    /**
    @notice Basic getter for a claim. 
    @dev Could be using public claims var, but this is cleaner in terms of naming. (getClaim(address) as opposed to claims(address)). 
    @param _recipient - the address for which we fetch the claim.
     */
    function getClaim(address _recipient) external view returns (Claim memory) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L86

9. missing: '@param _recipient'
```
    /**
    @notice This modifier requires that an user has a claim attached.
    @dev  To determine this, we check that a claim:
    * - is active
    * - start timestamp is nonzero.
    * These are sufficient conditions because we only ever set startTimestamp in 
    * createClaim, and we never change it. Therefore, startTimestamp will be set
    * IFF a claim has been created. In addition to that, we need to check
    * a claim is active (since this is has_*Active*_Claim)
    */
    modifier hasActiveClaim(address _recipient) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L95

10. missing: '@param _recipient'
```
    /** 
    @notice Modifier which is opposite hasActiveClaim
    @dev Requires that all fields are unset
    */ 
    modifier hasNoClaim(address _recipient) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L119

11. missing: '@return'
```
    /**
    @notice Pure function to calculate the vested amount from a given _claim, at a reference timestamp
    @param _claim The claim in question
    @param _referenceTs Timestamp for which we're calculating
     */
    function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L142

12. missing: '@return'
```
    /**
    @notice Calculate the amount vested for a given _recipient at a reference timestamp.
    @param _recipient - The address for whom we're calculating
    @param _referenceTs - The timestamp at which we want to calculate the vested amount.
    @dev Simply call the _baseVestedAmount for the claim in question
    */
    function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L190

13. missing: '@return'
```
    /**
    @notice Calculate the total vested at the end of the schedule, by simply feeding in the end timestamp to the function above.
    @dev This fn is somewhat superfluous, should probably be removed.
    @param _recipient - The address for whom we're calculating
     */
    function finalVestedAmount(address _recipient) public view returns (uint112) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L201

14. missing: '@return'
```
    /**
    @notice Calculates how much can we claim, by subtracting the already withdrawn amount from the vestedAmount at this moment.
    @param _recipient - The address for whom we're calculating
    */
    function claimableAmount(address _recipient) external view returns (uint112) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L211

15. missing: '@return'
```
    /** 
    @notice Return all the addresses that have vesting schedules attached.
    */ 
    function allVestingRecipients() external view returns (address[] memory) {
        return vestingRecipients;
    }
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L220

16. missing: '@return'
```
    /** 
    @notice Get the total number of vesting recipients.
    */
    function numVestingRecipients() external view returns (uint256) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L227

17. missing: '@param _recipient'
```
    /** 
    @notice Allow an Owner to revoke a claim that is already active.
    @dev The requirement is that a claim exists and that it's active.
    */ 
    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L414
