## Cache storage variables

Caching can be done at 2 places:
* `_createClaimUnchecked()` for `numTokensReservedForVesting` that is being read twice (the 2nd is implicit at a `+=` assignment)
    * ~100 units saved per call
* `withdraw()` - the `usrClaim` variable is used a few times
    * ~600 units saved per call

Code diff:
```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..70f51d7 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -291,14 +291,15 @@ contract VTVLVesting is Context, AccessProtected {
         // Not necessary to use the more complex logic from _baseVestedAmount
         uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
 
+        uint112 _numTokensReservedForVesting = numTokensReservedForVesting;
         // Still no effects up to this point (and tokenAddress is selected by contract deployer and is immutable), so no reentrancy risk 
-        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
+        require(tokenAddress.balanceOf(address(this)) >= _numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
 
         // Done with checks
 
         // Effects limited to lines below
         claims[_recipient] = _claim; // store the claim
-        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
+        numTokensReservedForVesting = _numTokensReservedForVesting + allocatedAmount; // track the allocated amount
         vestingRecipients.push(_recipient); // add the vesting recipient to the list
         emit ClaimCreated(_recipient, _claim); // let everyone know
     }
@@ -365,20 +366,21 @@ contract VTVLVesting is Context, AccessProtected {
         // Get the message sender claim - if any
 
         Claim storage usrClaim = claims[_msgSender()];
+        Claim memory _usrClaimCached = usrClaim;
 
         // we can use block.timestamp directly here as reference TS, as the function itself will make sure to cap it to endTimestamp
         // Conversion of timestamp to uint40 should be safe since 48 bit allows for a lot of years.
-        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
+        uint112 allowance = _baseVestedAmount(_usrClaimCached, uint40(block.timestamp));
 
         // Make sure we didn't already withdraw more that we're allowed.
-        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
+        require(allowance > _usrClaimCached.amountWithdrawn, "NOTHING_TO_WITHDRAW");
 
         // Calculate how much can we withdraw (equivalent to the above inequality)
-        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
+        uint112 amountRemaining = allowance - _usrClaimCached.amountWithdrawn;
 
         // "Double-entry bookkeeping"
         // Carry out the withdrawal by noting the withdrawn amount, and by transferring the tokens.
-        usrClaim.amountWithdrawn += amountRemaining;
+        usrClaim.amountWithdrawn = _usrClaimCached.amountWithdrawn + amountRemaining;
         // Reduce the allocated amount since the following transaction pays out so the "debt" gets reduced
         numTokensReservedForVesting -= amountRemaining;
         

```


Gas report diff (`-` is before, `+` is after):
```diff

 ···················|······················|··············|·············|·············|···············|··············
 |  TestERC20Token  ·  transfer            ·       47587  ·      52339  ·      47809  ·           45  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
-|  VTVLVesting     ·  createClaim         ·      139603  ·     173947  ·     167756  ·           28  ·          -  │
+|  VTVLVesting     ·  createClaim         ·      139472  ·     173816  ·     167627  ·           28  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
-|  VTVLVesting     ·  createClaimsBatch   ·      181558  ·     387422  ·     284486  ·            3  ·          -  │
+|  VTVLVesting     ·  createClaimsBatch   ·      181427  ·     387029  ·     284224  ·            3  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
 |  VTVLVesting     ·  revokeClaim         ·           -  ·          -  ·      40827  ·            6  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
 |  VTVLVesting     ·  setAdmin            ·       26511  ·      48423  ·      37467  ·            4  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
-|  VTVLVesting     ·  withdraw            ·       61171  ·      78271  ·      72938  ·           10  ·          -  │
+|  VTVLVesting     ·  withdraw            ·       60598  ·      77698  ·      72365  ·           10  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
 |  VTVLVesting     ·  withdrawAdmin       ·       43156  ·      64960  ·      54058  ·            4  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
@@ -116,7 +118,7 @@
 ··········································|··············|·············|·············|···············|··············
 |  TestERC20Token                         ·           -  ·          -  ·    1193264  ·          4 %  ·          -  │
 ··········································|··············|·············|·············|···············|··············
-|  VTVLVesting                            ·     3740716  ·    3740728  ·    3740727  ·       12.5 %  ·          -  │
+|  VTVLVesting                            ·     3806813  ·    3806825  ·    3806824  ·       12.7 %  ·          -  │
 ·-----------------------------------------|--------------|-------------|-------------|---------------|-------------·
```

## Use custom errors

Using custom errors can save a significant amount of gas *when the function reverts* + reduces code size and saves deployment gas.
