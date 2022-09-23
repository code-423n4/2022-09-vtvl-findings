# Gas

## Remove `hasActiveClaim` and `hasNoClaim` modifier in favour of inline require to reuse the claim and save lot of gas

The modifier `hasActiveClaim` is use only two times, and it will save a justified amount of gas because you will have to do one SLOAD instead of two SLOAD to get the `Claim`.

Also the modifier `hasNoClaim` is used only once, and its just a simple require, use a inline require to save gas.

So my recommendation is to;

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..f990420 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -92,53 +92,6 @@ contract VTVLVesting is Context, AccessProtected {
         return claims[_recipient];
     }
 
-    /**
-    @notice This modifier requires that an user has a claim attached.
-    @dev  To determine this, we check that a claim:
-    * - is active
-    * - start timestamp is nonzero.
-    * These are sufficient conditions because we only ever set startTimestamp in 
-    * createClaim, and we never change it. Therefore, startTimestamp will be set
-    * IFF a claim has been created. In addition to that, we need to check
-    * a claim is active (since this is has_*Active*_Claim)
-    */
-    modifier hasActiveClaim(address _recipient) {
-        Claim storage _claim = claims[_recipient];
-        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
-
-        // We however still need the active check, since (due to the name of the function)
-        // we want to only allow active claims
-        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
-
-        // Save gas, omit further checks
-        // require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
-        // require(_claim.endTimestamp > 0, "NO_END_TIMESTAMP");
-        _;
-    }
-
-    /** 
-    @notice Modifier which is opposite hasActiveClaim
-    @dev Requires that all fields are unset
-    */ 
-    modifier hasNoClaim(address _recipient) {
-        Claim storage _claim = claims[_recipient];
-        // Start timestamp != 0 is a sufficient condition for a claim to exist
-        // This is because we only ever add claims (or modify startTs) in the createClaim function 
-        // Which requires that its input startTimestamp be nonzero
-        // So therefore, a zero value for this indicates the claim does not exist.
-        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
-        
-        // We don't even need to check for active to be unset, since this function only 
-        // determines that a claim hasn't been set
-        // require(_claim.isActive == false, "CLAIM_ALREADY_EXISTS");
-    
-        // Further checks aren't necessary (to save gas), as they're done at creation time (createClaim)
-        // require(_claim.endTimestamp == 0, "CLAIM_ALREADY_EXISTS");
-        // require(_claim.linearVestAmount + _claim.cliffAmount == 0, "CLAIM_ALREADY_EXISTS");
-        // require(_claim.amountWithdrawn == 0, "CLAIM_ALREADY_EXISTS");
-        _;
-    }
-
     /**
     @notice Pure function to calculate the vested amount from a given _claim, at a reference timestamp
     @param _claim The claim in question
@@ -250,7 +203,14 @@ contract VTVLVesting is Context, AccessProtected {
             uint40 _releaseIntervalSecs, 
             uint112 _linearVestAmount, 
             uint112 _cliffAmount
-                ) private  hasNoClaim(_recipient) {
+                ) private {
+        
+        Claim memory _claim = claims[_recipient];
+        // Start timestamp != 0 is a sufficient condition for a claim to exist
+        // This is because we only ever add claims (or modify startTs) in the createClaim function 
+        // Which requires that its input startTimestamp be nonzero
+        // So therefore, a zero value for this indicates the claim does not exist.
+        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
 
         require(_recipient != address(0), "INVALID_ADDRESS");
         require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
@@ -361,11 +321,15 @@ contract VTVLVesting is Context, AccessProtected {
     @notice Withdraw the full claimable balance.
     @dev hasActiveClaim throws off anyone without a claim.
      */
-    function withdraw() hasActiveClaim(_msgSender()) external {
-        // Get the message sender claim - if any
+    function withdraw() external {
+        Claim storage usrClaim = claims[msg.sender];
+        require(usrClaim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
 
-        Claim storage usrClaim = claims[_msgSender()];
+        // We however still need the active check, since (due to the name of the function)
+        // we want to only allow active claims
+        require(usrClaim.isActive == true, "NO_ACTIVE_CLAIM");
 
+        
         // we can use block.timestamp directly here as reference TS, as the function itself will make sure to cap it to endTimestamp
         // Conversion of timestamp to uint40 should be safe since 48 bit allows for a lot of years.
         uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
@@ -415,9 +379,17 @@ contract VTVLVesting is Context, AccessProtected {
     @notice Allow an Owner to revoke a claim that is already active.
     @dev The requirement is that a claim exists and that it's active.
     */ 
-    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {
+    function revokeClaim(address _recipient) external onlyAdmin {
         // Fetch the claim
         Claim storage _claim = claims[_recipient];
+
+        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
+
+        // We however still need the active check, since (due to the name of the function)
+        // we want to only allow active claims
+        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
+
+        
         // Calculate what the claim should finally vest to
         uint112 finalVestAmt = finalVestedAmount(_recipient);
```



## Remove `Context.sol` OZ lib and just use `msg.sender`

You are importing OZ lib, `Context.sol` just for get the `msg.sender` via [`_msgSender()`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Context.sol#L17-L19)

Avoid this and just use `msg.sender` to reduce contract size.

```diff
diff --git a/contracts/AccessProtected.sol b/contracts/AccessProtected.sol
index 188c0dd..e77c2dd 100644
--- a/contracts/AccessProtected.sol
+++ b/contracts/AccessProtected.sol
@@ -1,28 +1,27 @@
 //SPDX-License-Identifier: Unlicense
 pragma solidity 0.8.14;
 
-import "@openzeppelin/contracts/utils/Context.sol";
 import "@openzeppelin/contracts/access/Ownable.sol";
 
 /** 
 @title Access Limiter to multiple owner-specified accounts.
 @dev Exposes the onlyAdmin modifier, which will revert (ADMIN_ACCESS_REQUIRED) if the caller is not the owner nor the admin.
 */
-abstract contract AccessProtected is Context {
+abstract contract AccessProtected {
     mapping(address => bool) private _admins; // user address => admin? mapping
 
     event AdminAccessSet(address indexed _admin, bool _enabled);
 
     constructor() {
-        _admins[_msgSender()] = true;
-        emit AdminAccessSet(_msgSender(), true);
+        _admins[msg.sender] = true;
+        emit AdminAccessSet(msg.sender, true);
     }
 
     /**
      * Throws if called by any account that isn't an admin or an owner.
      */
     modifier onlyAdmin() {
-        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
+        require(_admins[msg.sender], "ADMIN_ACCESS_REQUIRED");
         _;
     }
 
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..7aeb97d 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -2,13 +2,12 @@
 pragma solidity 0.8.14;
 // Note: using solidity 0.8, SafeMath not needed any more
 
-import "@openzeppelin/contracts/utils/Context.sol";
 import "@openzeppelin/contracts/access/Ownable.sol";
 import "@openzeppelin/contracts/interfaces/IERC20.sol";
 import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
 import "./AccessProtected.sol";
 
-contract VTVLVesting is Context, AccessProtected {
+contract VTVLVesting is AccessProtected {
     using SafeERC20 for IERC20;
 
     /**
@@ -361,14 +360,14 @@ contract VTVLVesting is Context, AccessProtected {
     @notice Withdraw the full claimable balance.
     @dev hasActiveClaim throws off anyone without a claim.
      */
-    function withdraw() hasActiveClaim(_msgSender()) external {
+    function withdraw() hasActiveClaim(msg.sender) external {
         // Get the message sender claim - if any
 
-        Claim storage usrClaim = claims[_msgSender()];
+        Claim storage usrClaim = claims[msg.sender];
 
         // we can use block.timestamp directly here as reference TS, as the function itself will make sure to cap it to endTimestamp
         // Conversion of timestamp to uint40 should be safe since 48 bit allows for a lot of years.
-        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
+        uint112 allowance = vestedAmount(msg.sender, uint40(block.timestamp));
 
         // Make sure we didn't already withdraw more that we're allowed.
         require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
@@ -385,10 +384,10 @@ contract VTVLVesting is Context, AccessProtected {
         // After the "books" are set, transfer the tokens
         // Reentrancy note - internal vars have been changed by now
         // Also following Checks-effects-interactions pattern
-        tokenAddress.safeTransfer(_msgSender(), amountRemaining);
+        tokenAddress.safeTransfer(msg.sender, amountRemaining);
 
         // Let withdrawal known to everyone.
-        emit Claimed(_msgSender(), amountRemaining);
+        emit Claimed(msg.sender, amountRemaining);
     }
 
     /**
@@ -404,10 +403,10 @@ contract VTVLVesting is Context, AccessProtected {
         // Actually withdraw the tokens
         // Reentrancy note - this operation doesn't touch any of the internal vars, simply transfers
         // Also following Checks-effects-interactions pattern
-        tokenAddress.safeTransfer(_msgSender(), _amountRequested);
+        tokenAddress.safeTransfer(msg.sender, _amountRequested);
 
         // Let the withdrawal known to everyone
-        emit AdminWithdrawn(_msgSender(), _amountRequested);
+        emit AdminWithdrawn(msg.sender, _amountRequested);
     }
 
 
@@ -447,6 +446,6 @@ contract VTVLVesting is Context, AccessProtected {
         require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
         uint256 bal = _otherTokenAddress.balanceOf(address(this));
         require(bal > 0, "INSUFFICIENT_BALANCE");
-        _otherTokenAddress.safeTransfer(_msgSender(), bal);
+        _otherTokenAddress.safeTransfer(msg.sender, bal);
     }
 }
```

---

## Optimize the require to check `lenght` in a more efficient way

This could save you gas when adding multiple claims with [`createClaimsBatch`](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L333)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..3f0e3c4 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -341,11 +341,9 @@ contract VTVLVesting is Context, AccessProtected {
         external onlyAdmin {
         
         uint256 length = _recipients.length;
-        require(_startTimestamps.length == length &&
-                _endTimestamps.length == length &&
-                _cliffReleaseTimestamps.length == length &&
-                _releaseIntervalsSecs.length == length &&
-                _linearVestAmounts.length == length &&
+        require(_startTimestamps.length == _endTimestamps.length &&
+                _cliffReleaseTimestamps.length == _releaseIntervalsSecs.length &&
+                _linearVestAmounts.length == _cliffAmounts.length &&
                 _cliffAmounts.length == length, 
                 "ARRAY_LENGTH_MISMATCH"
         );

```



## Use unchecked on safe math operations

```diff
diff --git a/contracts/token/VariableSupplyERC20Token.sol b/contracts/token/VariableSupplyERC20Token.sol
index 1297a14..2279ff5 100644
--- a/contracts/token/VariableSupplyERC20Token.sol
+++ b/contracts/token/VariableSupplyERC20Token.sol
@@ -40,7 +40,8 @@ contract VariableSupplyERC20Token is ERC20, AccessProtected {
         if(mintableSupply > 0) {
             require(amount <= mintableSupply, "INVALID_AMOUNT");
             // We need to reduce the amount only if we're using the limit, if not just leave it be
-            mintableSupply -= amount;
+            // safe to use unchecked because of previus require
+            unchecked { mintableSupply -= amount; }
         }
         _mint(account, amount);
     }
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..ecfff6d 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -289,16 +289,21 @@ contract VTVLVesting is Context, AccessProtected {
         });
         // Our total allocation is simply the full sum of the two amounts, _cliffAmount + _linearVestAmount
         // Not necessary to use the more complex logic from _baseVestedAmount
-        uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
-
-        // Still no effects up to this point (and tokenAddress is selected by contract deployer and is immutable), so no reentrancy risk 
-        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
-
+        uint112 allocatedAmount;
+        unchecked {
+            // this is a safe add, claim is control by admin and doesnt win anything overflowding it
+            allocatedAmount = _cliffAmount + _linearVestAmount;
+            
+            // Still no effects up to this point (and tokenAddress is selected by contract deployer and is immutable), so no reentrancy risk 
+            require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
+       }
+
+ 
         // Done with checks
 
         // Effects limited to lines below
         claims[_recipient] = _claim; // store the claim
-        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
+        unchecked { numTokensReservedForVesting += allocatedAmount; } // track the allocated amount
         vestingRecipients.push(_recipient); // add the vesting recipient to the list
         emit ClaimCreated(_recipient, _claim); // let everyone know
     }
@@ -350,8 +355,10 @@ contract VTVLVesting is Context, AccessProtected {
                 "ARRAY_LENGTH_MISMATCH"
         );
 
-        for (uint256 i = 0; i < length; i++) {
-            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
+        unchecked {
+            for (uint256 i; i < length; i++) {
+                _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
+            }
         }

         // No need for separate emit, since createClaim will emit for each claim (and this function is merely a convenience/gas-saver for multiple claims creation)
@@ -374,13 +381,17 @@ contract VTVLVesting is Context, AccessProtected {
         require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
 
         // Calculate how much can we withdraw (equivalent to the above inequality)
-        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
-
-        // "Double-entry bookkeeping"
-        // Carry out the withdrawal by noting the withdrawn amount, and by transferring the tokens.
-        usrClaim.amountWithdrawn += amountRemaining;
-        // Reduce the allocated amount since the following transaction pays out so the "debt" gets reduced
-        numTokensReservedForVesting -= amountRemaining;
+        uint112 amountRemaining;
+        
+        unchecked {
+            amountRemaining = allowance - usrClaim.amountWithdrawn;
+
+            // "Double-entry bookkeeping"
+            // Carry out the withdrawal by noting the withdrawn amount, and by transferring the tokens.
+            usrClaim.amountWithdrawn += amountRemaining;
+            // Reduce the allocated amount since the following transaction pays out so the "debt" gets reduced
+            numTokensReservedForVesting -= amountRemaining;
+        }
         
         // After the "books" are set, transfer the tokens
         // Reentrancy note - internal vars have been changed by now
```


## Use `calldata` instead of `memory`

> For array parameters in functions, it is recommended to use calldata over memory , as this provides significant gas savings. For example, a summing function that loops over an input array can save roughly 1829 gas (~3.5%) by using calldata . And that's it — some info regarding data locations in Solidity.
[Source](https://medium.com/coinmonks/solidity-storage-vs-memory-vs-calldata-8c7e8c38bce#:~:text=For%20array%20parameters%20in%20functions,3.5%25)%20by%20using%20calldata%20.&text=And%20that's%20it%20%E2%80%94%20some%20info%20regarding%20data%20locations%20in%20Solidity.)


```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..39dc30e 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -331,10 +331,10 @@ contract VTVLVesting is Context, AccessProtected {
     
      */
     function createClaimsBatch(
-        address[] memory _recipients, 
-        uint40[] memory _startTimestamps, 
-        uint40[] memory _endTimestamps, 
-        uint40[] memory _cliffReleaseTimestamps, 
+        address[] calldata _recipients, 
+        uint40[] calldata _startTimestamps, 
+        uint40[] calldata _endTimestamps, 
+        uint40[] calldata _cliffReleaseTimestamps, 
         uint40[] memory _releaseIntervalsSecs, 
         uint112[] memory _linearVestAmounts, 
         uint112[] memory _cliffAmounts) 
```

---

## Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

See more in
- https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth

