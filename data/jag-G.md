https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol
[G-01] No need to initialize variables (either storage, memory, local) to 0.
[G-02] Use pre increment in "for loop", ++i instead of i++
[G-03] Use Unchecked for pre increment in "for loop"
[G-04] Cache the storage variable

Create a local variable to cache numTokensReservedForVesting
and update it later. Saves one SLOAD operation.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295

[G-05] Use unchecked wherever arithmetic operation does not under/over flow

```git diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..3297c32 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -24,7 +24,7 @@ contract VTVLVesting is Context, AccessProtected {
     * In other words, this represents the amount the contract is scheduled to pay out at some point if the 
     * owner were to never interact with the contract.
     */
-    uint112 public numTokensReservedForVesting = 0;
+    uint112 public numTokensReservedForVesting;
 
     /**
     @notice A structure representing a single claim - supporting linear and cliff vesting.
@@ -36,7 +36,7 @@ contract VTVLVesting is Context, AccessProtected {
         uint40 endTimestamp; // When does the vesting end - the vesting goes linearly between the start and end timestamps
         uint40 cliffReleaseTimestamp; // At which timestamp is the cliffAmount released. This must be <= startTimestamp
         uint40 releaseIntervalSecs; // Every how many seconds does the vested amount increase. 
-        
+
         // uint112 range: range 0 –     5,192,296,858,534,827,628,530,496,329,220,095.
         // uint112 range: range 0 –                             5,192,296,858,534,827.
         uint112 linearVestAmount; // total entitlement
@@ -145,7 +145,7 @@ contract VTVLVesting is Context, AccessProtected {
     @param _referenceTs Timestamp for which we're calculating
      */
     function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
-        uint112 vestAmt = 0;
+        uint112 vestAmt;
         
         // the condition to have anything vested is to be active
         if(_claim.isActive) {
@@ -158,7 +158,9 @@ contract VTVLVesting is Context, AccessProtected {
             // If we're past the cliffReleaseTimestamp, we release the cliffAmount
             // We don't check here that cliffReleaseTimestamp is after the startTimestamp 
             if(_referenceTs >= _claim.cliffReleaseTimestamp) {
-                vestAmt += _claim.cliffAmount;
+                unchecked {
+                    vestAmt += _claim.cliffAmount;
+                }
             }
 
             // Calculate the linearly vested amount - this is relevant only if we're past the schedule start
@@ -176,7 +178,9 @@ contract VTVLVesting is Context, AccessProtected {
                 uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;
 
                 // Having calculated the linearVestAmount, simply add it to the vested amount
-                vestAmt += linearVestAmount;
+                unchecked {
+                    vestAmt += linearVestAmount;
+                }
             }
         }
         
@@ -289,16 +293,23 @@ contract VTVLVesting is Context, AccessProtected {
         });
         // Our total allocation is simply the full sum of the two amounts, _cliffAmount + _linearVestAmount
         // Not necessary to use the more complex logic from _baseVestedAmount
-        uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
+        uint112 allocatedAmount;
+        unchecked {
+            allocatedAmount = _cliffAmount + _linearVestAmount;
+        } 
 
+        //Cache the storage variable
+        uint112 _numTokensReservedForVesting = numTokensReservedForVesting;
         // Still no effects up to this point (and tokenAddress is selected by contract deployer and is immutable), so no reentrancy risk 
-        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
+        require(tokenAddress.balanceOf(address(this)) >= _numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
 
         // Done with checks
 
         // Effects limited to lines below
         claims[_recipient] = _claim; // store the claim
-        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
+        unchecked {
+            numTokensReservedForVesting = _numTokensReservedForVesting + allocatedAmount; // track the allocated amount
+        }
         vestingRecipients.push(_recipient); // add the vesting recipient to the list
         emit ClaimCreated(_recipient, _claim); // let everyone know
     }
@@ -350,8 +361,11 @@ contract VTVLVesting is Context, AccessProtected {
                 "ARRAY_LENGTH_MISMATCH"
         );
 
-        for (uint256 i = 0; i < length; i++) {
+        for (uint256 i; i < length;) {
             _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
+            unchecked {
+                ++i;
+            }
         }
 
         // No need for separate emit, since createClaim will emit for each claim (and this function is merely a convenience/gas-saver for multiple claims creation)
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

[G-04] Cache the storage variable 
[G-05] Use unchecked wherever arithmetic operation does not under/over flow

```git diff
diff --git a/contracts/token/VariableSupplyERC20Token.sol b/contracts/token/VariableSupplyERC20Token.sol
index 1297a14..c535e47 100644
--- a/contracts/token/VariableSupplyERC20Token.sol
+++ b/contracts/token/VariableSupplyERC20Token.sol
@@ -37,10 +37,13 @@ contract VariableSupplyERC20Token is ERC20, AccessProtected {
         require(account != address(0), "INVALID_ADDRESS");
         // If we're using maxSupply, we need to make sure we respect it
         // mintableSupply = 0 means mint at will
-        if(mintableSupply > 0) {
-            require(amount <= mintableSupply, "INVALID_AMOUNT");
+        uint256 _mintableSupply = mintableSupply;
+        if(_mintableSupply > 0) {
+            require(amount <= _mintableSupply, "INVALID_AMOUNT");
             // We need to reduce the amount only if we're using the limit, if not just leave it be
-            mintableSupply -= amount;
+            unchecked {
+                mintableSupply = _mintableSupply - amount;
+            }
         }
         _mint(account, amount);
     }

```

