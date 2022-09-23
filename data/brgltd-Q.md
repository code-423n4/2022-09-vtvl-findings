# [NC-01] Use the latest version of solidity and OpenZeppelin and avoid floating the pragma

The contract `VariableSupplyERC20Token.sol` is floating the pragma version. If the contract is not intended to be used as library, floating the pragma should be avoided to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Also, the project is using OpenZeppelin v4.5.0. The latest stable version of OpenZeppelin (at 23 Sep. 2022) is 4.7.3.

Furthermore, the following contracts are using solidity v0.8.14.

```solidity
FullPremintERC20Token.sol,
AccessProtected.sol,
VTVLVesting.sol
```

Consider updating solidity to 0.8.15 or 0.8.16 and also updaging OpenZeppelin to  ensure the latest security fixes at the compiler and library level are included in the contracts.

# [NC-02] Emit an event for critical functions

The function `withdrawOtherToken` could emit an even when a succesful transfer occrurs to facilitade off chain monitoring.

```solidity
function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
    require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
    uint256 bal = _otherTokenAddress.balanceOf(address(this));
    require(bal > 0, "INSUFFICIENT_BALANCE");
    _otherTokenAddress.safeTransfer(_msgSender(), bal);
}
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L446-L450

# [NC-03] Replace memory with calldata for read-only, non-primitve function arguments

```solidity
function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L147

```solidity
function createClaimsBatch(
    address[] memory _recipients, 
    uint40[] memory _startTimestamps, 
    uint40[] memory _endTimestamps, 
    uint40[] memory _cliffReleaseTimestamps, 
    uint40[] memory _releaseIntervalsSecs, 
    uint112[] memory _linearVestAmounts, 
    uint112[] memory _cliffAmounts) 
```

Calldata is recommended for arrays and structs when the inputs are not modified. This can also yield gas savings.

# [NC-04] Missing NATSPEC

```solidity
function mint(address account, uint256 amount) public onlyAdmin {
    require(account != address(0), "INVALID_ADDRESS");
    // If we're using maxSupply, we need to make sure we respect it
    // mintableSupply = 0 means mint at will
    if(mintableSupply > 0) {
        require(amount <= mintableSupply, "INVALID_AMOUNT");
        // We need to reduce the amount only if we're using the limit, if not just leave it be
        mintableSupply -= amount;
    }
    _mint(account, amount);
}
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36-L46

# [NC-05] TODOs should be resolved before deployment

```solidity
// Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

# [NC-06] Replace public with external for functions not called by the contract

```solidity
function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398

```solidity
function setAdmin(address admin, bool isEnabled) public onlyAdmin {
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39

# [NC-07] Repeated validation can be refactored into a single modifier

The checks for validating an input address against address zero can be refactored into a single modifier to improve code reusability.

```solidity
$ git diff --no-index VTVLVesting.sol.orig VTVLVesting.sol
diff --git a/VTVLVesting.sol.orig b/VTVLVesting.sol
index 3e10653..42e95b8 100644
--- a/VTVLVesting.sol.orig
+++ b/VTVLVesting.sol
@@ -73,13 +73,17 @@ contract VTVLVesting is Context, AccessProtected {

+    modifier addrNotZero(address _addr) {
+        require(_addr != address(0), "zero address");
+        _;
+    }
+
     //
     /**
     @notice Construct the contract, taking the ERC20 token to be vested as the parameter.
     @dev The owner can set the contract in question when creating the contract.
      */
-    constructor(IERC20 _tokenAddress) {
-        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
+    constructor(IERC20 _tokenAddress) addrNotZero(_tokenAddress) {
         tokenAddress = _tokenAddress;
     }

@@ -250,9 +254,8 @@ contract VTVLVesting is Context, AccessProtected {
             uint40 _releaseIntervalSecs,
             uint112 _linearVestAmount,
             uint112 _cliffAmount
-                ) private  hasNoClaim(_recipient) {
+                ) private  hasNoClaim(_recipient) addrNotZero(_recipient) {

-        require(_recipient != address(0), "INVALID_ADDRESS");
         require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
         require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
         // Do we need to check whether _startTimestamp is greater than the current block.timestamp?

```

# [NC-08] Limit the number of maximum characters per line

Some lines have 120+ characters. Setting a maximum width per line would improve code readbility.

```
uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L169

```
event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L69

# [NC-09] Normalize the spacing for functions with multiple arguments

```solidity
function createclaim(
        address _recipient, 
        uint40 _starttimestamp, 
        uint40 _endtimestamp, 
        uint40 _cliffreleasetimestamp, 
        uint40 _releaseintervalsecs, 
        uint112 _linearvestamount, 
        uint112 _cliffamount
            ) external onlyadmin {
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L317-L325

```solidity
function createClaimsBatch(
    address[] memory _recipients, 
    uint40[] memory _startTimestamps, 
    uint40[] memory _endTimestamps, 
    uint40[] memory _cliffReleaseTimestamps, 
    uint40[] memory _releaseIntervalsSecs, 
    uint112[] memory _linearVestAmounts, 
    uint112[] memory _cliffAmounts) 
    external onlyAdmin {
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L333-L341

Note that `createClaim` has twice as many spaces than `createClaimBatch` for it's function arguments. Also note that the ending parenthesis is not following the same pattern.

Consider using only one coding style, ideally with the usage a formatter and linter (e.g. prettier and solhint).