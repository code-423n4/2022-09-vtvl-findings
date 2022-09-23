##### Gas Optimizations

Gas savings are estimated using the gas report of existing `npx hardhat test` tests (the sum of all deployment costs and the sum of the costs of calling methods) and may vary depending on the implementation of the fix.

|        | Issue                                                                                                                   | Instances | Estimated gas(deployments) | Estimated gas(avg method call) |
| :----: | :---------------------------------------------------------------------------------------------------------------------- | :-------: | :------------------------: | :----------------------------: |
| **1**  | Use custom errors rather than revert()/require() strings to save gas                                                    |    23     |          401 938           |               24               |
| **2**  | Use functions instead of modifiers                                                                                      |     3     |          192 776           |              255               |
| **3**  | Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead.                                                   |     1     |           36 153           |             1 137              |
| **4**  | Expression can be unchecked when overflow is not possible.                                                              |     1     |           17 316           |              266               |
| **5**  | State variables should be cached in stack variables rather than re-reading them from storage                            |     6     |           14 149           |              384               |
| **6**  | Using bools for storage incurs overhead                                                                                 |     1     |           15 697           |              204               |
| **7**  | It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied |     2     |           3 995            |               16               |
| **8**  | Don't compare boolean expressions to boolean literals                                                                   |     1     |           3 056            |               48               |
| **9**  | Don't cache a variable that will only be used once                                                                      |     2     |           2 388            |               65               |
| **10** | ++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)                                       |     1     |            444             |               18               |
| **11** | `x = x + y` is cheaper than `x += y`                                                                                    |     7     |            444             |               42               |
|        | **Overall Gas Savings**                                                                                                 |  **48**   |        **660 826**(13,39%)         |           **1 849**(0,24%)            |

**Total: 48 instances over 11 issues**

---

#### 1. **Use custom errors rather than revert()/require() strings to save gas (23 instances)**

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

##### Gas Saved:

Deployements:
**401 938**
Method Calls:
**24**

##### - contracts/VTVLVesting.sol:[82](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L782), [107](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107), [111](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111), [129](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L129), [255-257](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255-L257), [262-264](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L262-L264), [270-278](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270-L278), [295](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295), [344-351](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L351), [374](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L374), [402](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L402), [426](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426), [447](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L447), [449](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..570636c 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -8,6 +8,21 @@ import "@openzeppelin/contracts/interfaces/IERC20.sol";
    8,   8: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
    9,   9: import "./AccessProtected.sol";
   10,  10:
+       11:+error InsufficientBalance();
+       12:+error InvalidToken();
+       13:+error NoUnvestedAmount();
+       14:+error NothingToWithdraw();
+       15:+error ArrayLengthMismatch();
+       16:+error InvalidCliff();
+       17:+error InvalidIntervalLength();
+       18:+error InvalidEndTimestamp();
+       19:+error InvalidReleaseInterval();
+       20:+error InvalidStartTimestamp();
+       21:+error InvalidVestedAmount();
+       22:
+       23:+error ClaimAlreadyExists();
+       24:+error NoActiveClaim();
+       25:+
   11,  26: contract VTVLVesting is Context, AccessProtected {
   12,  27:     using SafeERC20 for IERC20;
   13,  28:
@@ -79,7 +94,7 @@ contract VTVLVesting is Context, AccessProtected {
   79,  94:     @dev The owner can set the contract in question when creating the contract.
   80,  95:      */
   81,  96:     constructor(IERC20 _tokenAddress) {
-  82     :-        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
+       97:+        if(address(_tokenAddress) == address(0)) revert InvalidAddress();
   83,  98:         tokenAddress = _tokenAddress;
   84,  99:     }
   85, 100:
@@ -104,11 +119,11 @@ contract VTVLVesting is Context, AccessProtected {
  104, 119:     */
  105, 120:     modifier hasActiveClaim(address _recipient) {
  106, 121:         Claim storage _claim = claims[_recipient];
- 107     :-        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
+      122:+        if(_claim.startTimestamp == 0) revert NoActiveClaim();
  108, 123:
  109, 124:         // We however still need the active check, since (due to the name of the function)
  110, 125:         // we want to only allow active claims
- 111     :-        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
+      126:+        if(!_claim.isActive) revert NoActiveClaim();
  112, 127:
  113, 128:         // Save gas, omit further checks
  114, 129:         // require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
@@ -126,7 +141,7 @@ contract VTVLVesting is Context, AccessProtected {
  126, 141:         // This is because we only ever add claims (or modify startTs) in the createClaim function
  127, 142:         // Which requires that its input startTimestamp be nonzero
  128, 143:         // So therefore, a zero value for this indicates the claim does not exist.
- 129     :-        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
+      144:+        if(_claim.startTimestamp != 0) revert ClaimAlreadyExists();
  130, 145:
  131, 146:         // We don't even need to check for active to be unset, since this function only
  132, 147:         // determines that a claim hasn't been set
@@ -252,30 +267,30 @@ contract VTVLVesting is Context, AccessProtected {
  252, 267:             uint112 _cliffAmount
  253, 268:                 ) private  hasNoClaim(_recipient) {
  254, 269:
- 255     :-        require(_recipient != address(0), "INVALID_ADDRESS");
- 256     :-        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
- 257     :-        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
+      270:+        if(_recipient == address(0)) revert InvalidAddress();
+      271:+        if(_linearVestAmount + _cliffAmount == 0) revert InvalidVestedAmount(); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
+      272:+        if(_startTimestamp == 0) revert InvalidStartTimestamp();
  258, 273:         // Do we need to check whether _startTimestamp is greater than the current block.timestamp?
  259, 274:         // Or do we allow schedules that started in the past?
  260, 275:         // -> Conclusion: we want to allow this, for founders that might have forgotten to add some users, or to avoid issues with transactions not going through because of discoordination between block.timestamp and sender's local time
  261, 276:         // require(_endTimestamp > 0, "_endTimestamp must be valid"); // not necessary because of the next condition (transitively)
- 262     :-        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
- 263     :-        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
- 264     :-        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
+      277:+        if(_startTimestamp >= _endTimestamp) revert InvalidEndTimestamp(); // _endTimestamp must be after _startTimestamp
+      278:+        if(_releaseIntervalSecs == 0) revert InvalidReleaseInterval();
+      279:+        if((_endTimestamp - _startTimestamp) % _releaseIntervalSecs != 0) revert InvalidIntervalLength();
  265, 280:
  266, 281:         // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
  267, 282:
  268, 283:         // No point in allowing cliff TS without the cliff amount or vice versa.
  269, 284:         // Both or neither of _cliffReleaseTimestamp and _cliffAmount must be set. If cliff is set, _cliffReleaseTimestamp must be before or at the _startTimestamp
- 270     :-        require(
+      285:+        if(
  271, 286:             (
- 272     :-                _cliffReleaseTimestamp > 0 &&
- 273     :-                _cliffAmount > 0 &&
- 274     :-                _cliffReleaseTimestamp <= _startTimestamp
- 275     :-            ) || (
- 276     :-                _cliffReleaseTimestamp == 0 &&
- 277     :-                _cliffAmount == 0
- 278     :-        ), "INVALID_CLIFF");
+      287:+                _cliffReleaseTimestamp <= 0 ||
+      288:+                _cliffAmount <= 0 ||
+      289:+                _cliffReleaseTimestamp > _startTimestamp
+      290:+            ) && (
+      291:+                _cliffReleaseTimestamp != 0 ||
+      292:+                _cliffAmount != 0
+      293:+        )) revert InvalidCliff();
  279, 294:
  280, 295:         Claim memory _claim = Claim({
  281, 296:             startTimestamp: _startTimestamp,
@@ -292,7 +307,7 @@ contract VTVLVesting is Context, AccessProtected {
  292, 307:         uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
  293, 308:
  294, 309:         // Still no effects up to this point (and tokenAddress is selected by contract deployer and is immutable), so no reentrancy risk
- 295     :-        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
+      310:+        if(tokenAddress.balanceOf(address(this)) < numTokensReservedForVesting + allocatedAmount) revert InsufficientBalance();
  296, 311:
  297, 312:         // Done with checks
  298, 313:
@@ -341,14 +356,12 @@ contract VTVLVesting is Context, AccessProtected {
  341, 356:         external onlyAdmin {
  342, 357:
  343, 358:         uint256 length = _recipients.length;
- 344     :-        require(_startTimestamps.length == length &&
- 345     :-                _endTimestamps.length == length &&
- 346     :-                _cliffReleaseTimestamps.length == length &&
- 347     :-                _releaseIntervalsSecs.length == length &&
- 348     :-                _linearVestAmounts.length == length &&
- 349     :-                _cliffAmounts.length == length,
- 350     :-                "ARRAY_LENGTH_MISMATCH"
- 351     :-        );
+      359:+        if(_startTimestamps.length != length ||
+      360:+                _endTimestamps.length != length ||
+      361:+                _cliffReleaseTimestamps.length != length ||
+      362:+                _releaseIntervalsSecs.length != length ||
+      363:+                _linearVestAmounts.length != length ||
+      364:+                _cliffAmounts.length != length) revert ArrayLengthMismatch();
  352, 365:
  353, 366:         for (uint256 i = 0; i < length; i++) {
  354, 367:             _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
@@ -371,7 +384,7 @@ contract VTVLVesting is Context, AccessProtected {
  371, 384:         uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
  372, 385:
  373, 386:         // Make sure we didn't already withdraw more that we're allowed.
- 374     :-        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
+      387:+        if(allowance <= usrClaim.amountWithdrawn) revert NothingToWithdraw();
  375, 388:
  376, 389:         // Calculate how much can we withdraw (equivalent to the above inequality)
  377, 390:         uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
@@ -399,7 +412,7 @@ contract VTVLVesting is Context, AccessProtected {
  399, 412:         // Allow the owner to withdraw any balance not currently tied up in contracts.
  400, 413:         uint256 amountRemaining = tokenAddress.balanceOf(address(this)) - numTokensReservedForVesting;
  401, 414:
- 402     :-        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
+      415:+        if(amountRemaining < _amountRequested) revert InsufficientBalance();
  403, 416:
  404, 417:         // Actually withdraw the tokens
  405, 418:         // Reentrancy note - this operation doesn't touch any of the internal vars, simply transfers
@@ -423,7 +436,7 @@ contract VTVLVesting is Context, AccessProtected {
  423, 436:
  424, 437:         // No point in revoking something that has been fully consumed
  425, 438:         // so require that there be unconsumed amount
- 426     :-        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
+      439:+        if( _claim.amountWithdrawn >= finalVestAmt) revert NoUnvestedAmount();
  427, 440:
  428, 441:         // The amount that is "reclaimed" is equal to the total allocation less what was already withdrawn
  429, 442:         uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
@@ -444,9 +457,9 @@ contract VTVLVesting is Context, AccessProtected {
  444, 457:     @param _otherTokenAddress - the token which we want to withdraw
  445, 458:      */
  446, 459:     function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
- 447     :-        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
+      460:+        if(_otherTokenAddress == tokenAddress) revert InvalidToken(); // tokenAddress address is already sure to be nonzero due to constructor
  448, 461:         uint256 bal = _otherTokenAddress.balanceOf(address(this));
- 449     :-        require(bal > 0, "INSUFFICIENT_BALANCE");
+      462:+        if(bal == 0) revert InsufficientBalance();
  450, 463:         _otherTokenAddress.safeTransfer(_msgSender(), bal);
  451, 464:     }
  452, 465: }
```

##### - contracts/AccessProtected.sol:[25](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L25), [40](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L40)

```diff
diff --git a/contracts/AccessProtected.sol b/contracts/AccessProtected.sol
index 188c0dd..af98d15 100644
--- a/contracts/AccessProtected.sol
+++ b/contracts/AccessProtected.sol
@@ -4,6 +4,9 @@ pragma solidity 0.8.14;
    4,   4: import "@openzeppelin/contracts/utils/Context.sol";
    5,   5: import "@openzeppelin/contracts/access/Ownable.sol";
    6,   6:
+        7:+error AdminAccessRequired();
+        8:+error InvalidAddress();
+        9:+
    7,  10: /**
    8,  11: @title Access Limiter to multiple owner-specified accounts.
    9,  12: @dev Exposes the onlyAdmin modifier, which will revert (ADMIN_ACCESS_REQUIRED) if the caller is not the owner nor the admin.
@@ -22,7 +25,7 @@ abstract contract AccessProtected is Context {
   22,  25:      * Throws if called by any account that isn't an admin or an owner.
   23,  26:      */
   24,  27:     modifier onlyAdmin() {
-  25     :-        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
+       28:+        if(!_admins[_msgSender()]) revert AdminAccessRequired();
   26,  29:         _;
   27,  30:     }
   28,  31:
@@ -37,7 +40,7 @@ abstract contract AccessProtected is Context {
   37,  40:      * @param isEnabled - Enable/Disable Admin Access
   38,  41:      */
   39,  42:     function setAdmin(address admin, bool isEnabled) public onlyAdmin {
-  40     :-        require(admin != address(0), "INVALID_ADDRESS");
+       43:+        if(admin == address(0)) revert InvalidAddress();
   41,  44:         _admins[admin] = isEnabled;
   42,  45:         emit AdminAccessSet(admin, isEnabled);
   43,  46:     }
```

##### - contracts/token/FullPremintERC20Token.sol:[11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11)

```diff
diff --git a/contracts/token/FullPremintERC20Token.sol b/contracts/token/FullPremintERC20Token.sol
index ce22a3d..aebbc67 100644
--- a/contracts/token/FullPremintERC20Token.sol
+++ b/contracts/token/FullPremintERC20Token.sol
@@ -3,12 +3,14 @@ pragma solidity 0.8.14;
    3,   3:
    4,   4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
    5,   5:
+        6:+error ZeroMint();
+        7:+
    6,   8: // Mint everything at once
    7,   9: // VariableSupplyERC20Token could be used instead, but it needs to track available to mint supply (extra slot)
    8,  10: contract FullPremintERC20Token is ERC20 {
    9,  11:     // uint constant _initialSupply = 100 * (10**18);
   10,  12:     constructor(string memory name_, string memory symbol_, uint256 supply_) ERC20(name_, symbol_) {
-  11     :-        require(supply_ > 0, "NO_ZERO_MINT");
+       13:+        if(supply_ == 0) revert ZeroMint();
   12,  14:         _mint(_msgSender(), supply_);
   13,  15:     }
   14,  16: }
\ No newline at end of file
```

##### - contracts/token/VariableSupplyERC20Token.sol:[27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27), [37](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L37), [41](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41)

```diff
diff --git a/contracts/token/VariableSupplyERC20Token.sol b/contracts/token/VariableSupplyERC20Token.sol
index 1297a14..584da3f 100644
--- a/contracts/token/VariableSupplyERC20Token.sol
+++ b/contracts/token/VariableSupplyERC20Token.sol
@@ -4,6 +4,8 @@ pragma solidity ^0.8.14;
    4,   4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
    5,   5: import "../AccessProtected.sol";
    6,   6:
+        7:+error InvalidAmount();
+        8:+
    7,   9: /**
    8,  10: @notice A ERC20 token contract that allows minting at will, with limited or unlimited supply.
    9,  11:  */
@@ -24,7 +26,7 @@ contract VariableSupplyERC20Token is ERC20, AccessProtected {
   24,  26:         // Therefore, we have valid scenarios if either of them is 0
   25,  27:         // However, if both are 0 we might have a valid scenario as well - user just wants to create a token but doesn't want to mint anything
   26,  28:         // Should we allow this?
-  27     :-        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
+       29:+        if(initialSupply_ == 0 && maxSupply_ == 0) revert InvalidAmount();
   28,  30:         mintableSupply = maxSupply_;
   29,  31:
   30,  32:         // Note: the check whether initial supply is less than or equal than mintableSupply will happen in mint fn.
@@ -34,11 +36,11 @@ contract VariableSupplyERC20Token is ERC20, AccessProtected {
   34,  36:     }
   35,  37:
   36,  38:     function mint(address account, uint256 amount) public onlyAdmin {
-  37     :-        require(account != address(0), "INVALID_ADDRESS");
+       39:+        if(account == address(0)) revert InvalidAddress();
   38,  40:         // If we're using maxSupply, we need to make sure we respect it
   39,  41:         // mintableSupply = 0 means mint at will
   40,  42:         if(mintableSupply > 0) {
-  41     :-            require(amount <= mintableSupply, "INVALID_AMOUNT");
+       43:+            if(amount > mintableSupply) revert InvalidAmount();
   42,  44:             // We need to reduce the amount only if we're using the limit, if not just leave it be
   43,  45:             mintableSupply -= amount;
   44,  46:         }
```

#### 2. **Use function instead of modifiers (3 instances)**

##### Gas Saved:

Deployements:
**192 776**
Method Calls:
**-255**

##### - contracts/AccessProtected.sol:[24](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L24)

```diff
diff --git a/contracts/AccessProtected.sol b/contracts/AccessProtected.sol
index 188c0dd..a48951f 100644
--- a/contracts/AccessProtected.sol
+++ b/contracts/AccessProtected.sol
@@ -21,9 +21,8 @@ abstract contract AccessProtected is Context {
   21,  21:     /**
   22,  22:      * Throws if called by any account that isn't an admin or an owner.
   23,  23:      */
-  24     :-    modifier onlyAdmin() {
+       24:+    function onlyAdmin() view internal {
   25,  25:         require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
-  26     :-        _;
   27,  26:     }
   28,  27:
   29,  28:     function isAdmin(address _addressToCheck) external view returns (bool) {
@@ -36,7 +35,8 @@ abstract contract AccessProtected is Context {
   36,  35:      * @param admin - Address of the new admin (or the one to be removed)
   37,  36:      * @param isEnabled - Enable/Disable Admin Access
   38,  37:      */
-  39     :-    function setAdmin(address admin, bool isEnabled) public onlyAdmin {
+       38:+    function setAdmin(address admin, bool isEnabled) public {
+       39:+        onlyAdmin();
   40,  40:         require(admin != address(0), "INVALID_ADDRESS");
   41,  41:         _admins[admin] = isEnabled;
   42,  42:         emit AdminAccessSet(admin, isEnabled);
```

##### - contracts/VTVLVesting.sol:[105](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L105), [123](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L123)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..c90d9e2 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -102,7 +102,7 @@ contract VTVLVesting is Context, AccessProtected {
  102, 102:     * IFF a claim has been created. In addition to that, we need to check
  103, 103:     * a claim is active (since this is has_*Active*_Claim)
  104, 104:     */
- 105     :-    modifier hasActiveClaim(address _recipient) {
+      105:+    function hasActiveClaim(address _recipient) view private {
  106, 106:         Claim storage _claim = claims[_recipient];
  107, 107:         require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
  108, 108:
@@ -113,14 +113,13 @@ contract VTVLVesting is Context, AccessProtected {
  113, 113:         // Save gas, omit further checks
  114, 114:         // require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
  115, 115:         // require(_claim.endTimestamp > 0, "NO_END_TIMESTAMP");
- 116     :-        _;
  117, 116:     }
  118, 117:
  119, 118:     /**
  120, 119:     @notice Modifier which is opposite hasActiveClaim
  121, 120:     @dev Requires that all fields are unset
  122, 121:     */
- 123     :-    modifier hasNoClaim(address _recipient) {
+      122:+    function hasNoClaim(address _recipient) view private {
  124, 123:         Claim storage _claim = claims[_recipient];
  125, 124:         // Start timestamp != 0 is a sufficient condition for a claim to exist
  126, 125:         // This is because we only ever add claims (or modify startTs) in the createClaim function
@@ -136,7 +135,6 @@ contract VTVLVesting is Context, AccessProtected {
  136, 135:         // require(_claim.endTimestamp == 0, "CLAIM_ALREADY_EXISTS");
  137, 136:         // require(_claim.linearVestAmount + _claim.cliffAmount == 0, "CLAIM_ALREADY_EXISTS");
  138, 137:         // require(_claim.amountWithdrawn == 0, "CLAIM_ALREADY_EXISTS");
- 139     :-        _;
  140, 138:     }
  141, 139:
  142, 140:     /**
@@ -250,7 +248,8 @@ contract VTVLVesting is Context, AccessProtected {
  250, 248:             uint40 _releaseIntervalSecs,
  251, 249:             uint112 _linearVestAmount,
  252, 250:             uint112 _cliffAmount
- 253     :-                ) private  hasNoClaim(_recipient) {
+      251:+                ) private   {
+      252:+                    hasNoClaim(_recipient);
  254, 253:
  255, 254:         require(_recipient != address(0), "INVALID_ADDRESS");
  256, 255:         require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
@@ -322,7 +321,8 @@ contract VTVLVesting is Context, AccessProtected {
  322, 321:             uint40 _releaseIntervalSecs,
  323, 322:             uint112 _linearVestAmount,
  324, 323:             uint112 _cliffAmount
- 325     :-                ) external onlyAdmin {
+      324:+                ) external  {
+      325:+                    onlyAdmin();
  326, 326:         _createClaimUnchecked(_recipient, _startTimestamp, _endTimestamp, _cliffReleaseTimestamp, _releaseIntervalSecs, _linearVestAmount, _cliffAmount);
  327, 327:     }
  328, 328:
@@ -338,7 +338,8 @@ contract VTVLVesting is Context, AccessProtected {
  338, 338:         uint40[] memory _releaseIntervalsSecs,
  339, 339:         uint112[] memory _linearVestAmounts,
  340, 340:         uint112[] memory _cliffAmounts)
- 341     :-        external onlyAdmin {
+      341:+        external  {
+      342:+            onlyAdmin();
  342, 343:
  343, 344:         uint256 length = _recipients.length;
  344, 345:         require(_startTimestamps.length == length &&
@@ -361,7 +362,8 @@ contract VTVLVesting is Context, AccessProtected {
  361, 362:     @notice Withdraw the full claimable balance.
  362, 363:     @dev hasActiveClaim throws off anyone without a claim.
  363, 364:      */
- 364     :-    function withdraw() hasActiveClaim(_msgSender()) external {
+      365:+    function withdraw() external {
+      366:+         hasActiveClaim(_msgSender());
  365, 367:         // Get the message sender claim - if any
  366, 368:
  367, 369:         Claim storage usrClaim = claims[_msgSender()];
@@ -395,7 +397,8 @@ contract VTVLVesting is Context, AccessProtected {
  395, 397:     @notice Admin withdrawal of the unallocated tokens.
  396, 398:     @param _amountRequested - the amount that we want to withdraw
  397, 399:      */
- 398     :-    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
+      400:+    function withdrawAdmin(uint112 _amountRequested) public  {
+      401:+        onlyAdmin();
  399, 402:         // Allow the owner to withdraw any balance not currently tied up in contracts.
  400, 403:         uint256 amountRemaining = tokenAddress.balanceOf(address(this)) - numTokensReservedForVesting;
  401, 404:
@@ -415,7 +418,9 @@ contract VTVLVesting is Context, AccessProtected {
  415, 418:     @notice Allow an Owner to revoke a claim that is already active.
  416, 419:     @dev The requirement is that a claim exists and that it's active.
  417, 420:     */
- 418     :-    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {
+      421:+    function revokeClaim(address _recipient) external   {
+      422:+        onlyAdmin();
+      423:+        hasActiveClaim(_recipient);
  419, 424:         // Fetch the claim
  420, 425:         Claim storage _claim = claims[_recipient];
  421, 426:         // Calculate what the claim should finally vest to
@@ -443,7 +448,8 @@ contract VTVLVesting is Context, AccessProtected {
  443, 448:     Note that the token to be withdrawn can't be the one at "tokenAddress".
  444, 449:     @param _otherTokenAddress - the token which we want to withdraw
  445, 450:      */
- 446     :-    function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
+      451:+    function withdrawOtherToken(IERC20 _otherTokenAddress) external  {
+      452:+        onlyAdmin();
  447, 453:         require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
  448, 454:         uint256 bal = _otherTokenAddress.balanceOf(address(this));
  449, 455:         require(bal > 0, "INSUFFICIENT_BALANCE");
diff --git a/contracts/token/VariableSupplyERC20Token.sol b/contracts/token/VariableSupplyERC20Token.sol
index 1297a14..042263b 100644
--- a/contracts/token/VariableSupplyERC20Token.sol
+++ b/contracts/token/VariableSupplyERC20Token.sol
@@ -33,7 +33,8 @@ contract VariableSupplyERC20Token is ERC20, AccessProtected {
   33,  33:         }
   34,  34:     }
   35,  35:
-  36     :-    function mint(address account, uint256 amount) public onlyAdmin {
+       36:+    function mint(address account, uint256 amount) public  {
+       37:+        onlyAdmin();
   37,  38:         require(account != address(0), "INVALID_ADDRESS");
   38,  39:         // If we're using maxSupply, we need to make sure we respect it
   39,  40:         // mintableSupply = 0 means mint at will
```

#### 3. **Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead. (1 instance)**

##### Gas Saved:

Deployements:
**36 153**
Method Calls:
**1 137**

Don't use a storage variable smaller than 32 bytes unless you are packing it with other variables.

##### - contracts/VTVLVesting.sol:[27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..1996a1e 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -24,7 +24,7 @@ contract VTVLVesting is Context, AccessProtected {
   24,  24:     * In other words, this represents the amount the contract is scheduled to pay out at some point if the
   25,  25:     * owner were to never interact with the contract.
   26,  26:     */
-  27     :-    uint112 public numTokensReservedForVesting = 0;
+       27:+    uint256 public numTokensReservedForVesting = 0;
   28,  28:
   29,  29:     /**
   30,  30:     @notice A structure representing a single claim - supporting linear and cliff vesting.
```

#### 4. **Expression can be unchecked when overflow is not possible. (1 instance)**

##### Gas Saved:

Deployements:
**17 316**
Method Calls:
**266**

##### - contracts/VTVLVesting.sol:[353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..72e23bf 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -350,8 +350,11 @@ contract VTVLVesting is Context, AccessProtected {
  350, 350:                 "ARRAY_LENGTH_MISMATCH"
  351, 351:         );
  352, 352:
- 353     :-        for (uint256 i = 0; i < length; i++) {
+      353:+        for (uint256 i = 0; i < length;) {
  354, 354:             _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
+      355:+            unchecked {
+      356:+                ++i;
+      357:+            }
  355, 358:         }
  356, 359:
  357, 360:         // No need for separate emit, since createClaim will emit for each claim (and this function is merely a convenience/gas-saver for multiple claims creation)
```

#### 5. **State variables should be cached in stack variables rather than re-reading them from storage (6 instances)**

##### Gas Saved:

Deployements:
**14 149**
Method Calls:
**384**

The code can be optimized by minimising the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

##### - contracts/AccessProtected.sol:[17-18](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L17-L18)

```diff
diff --git a/contracts/AccessProtected.sol b/contracts/AccessProtected.sol
index 188c0dd..aed5031 100644
--- a/contracts/AccessProtected.sol
+++ b/contracts/AccessProtected.sol
@@ -14,8 +14,9 @@ abstract contract AccessProtected is Context {
   14,  14:     event AdminAccessSet(address indexed _admin, bool _enabled);
   15,  15:
   16,  16:     constructor() {
-  17     :-        _admins[_msgSender()] = true;
-  18     :-        emit AdminAccessSet(_msgSender(), true);
+       17:+        address msgSender_;
+       18:+        _admins[(msgSender_ = _msgSender())] = true;
+       19:+        emit AdminAccessSet(msgSender_, true);
   19,  20:     }
   20,  21:
   21,  22:     /**
```

##### - contracts/VTVLVesting.sol:[367](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L367), [374](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374), [407](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L407), [426](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L426)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..8b8f24f 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -364,17 +364,19 @@ contract VTVLVesting is Context, AccessProtected {
  364, 364:     function withdraw() hasActiveClaim(_msgSender()) external {
  365, 365:         // Get the message sender claim - if any
  366, 366:
- 367     :-        Claim storage usrClaim = claims[_msgSender()];
+      367:+        address msgSender_;
+      368:+        Claim storage usrClaim = claims[(msgSender_ = _msgSender())];
  368, 369:
  369, 370:         // we can use block.timestamp directly here as reference TS, as the function itself will make sure to cap it to endTimestamp
  370, 371:         // Conversion of timestamp to uint40 should be safe since 48 bit allows for a lot of years.
- 371     :-        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
+      372:+        uint112 allowance = vestedAmount(msgSender_, uint40(block.timestamp));
  372, 373:
  373, 374:         // Make sure we didn't already withdraw more that we're allowed.
- 374     :-        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
+      375:+        uint112 _amountWithdrawn;
+      376:+        require(allowance > (_amountWithdrawn = usrClaim.amountWithdrawn), "NOTHING_TO_WITHDRAW");
  375, 377:
  376, 378:         // Calculate how much can we withdraw (equivalent to the above inequality)
- 377     :-        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
+      379:+        uint112 amountRemaining = allowance - _amountWithdrawn;
  378, 380:
  379, 381:         // "Double-entry bookkeeping"
  380, 382:         // Carry out the withdrawal by noting the withdrawn amount, and by transferring the tokens.
@@ -385,10 +387,10 @@ contract VTVLVesting is Context, AccessProtected {
  385, 387:         // After the "books" are set, transfer the tokens
  386, 388:         // Reentrancy note - internal vars have been changed by now
  387, 389:         // Also following Checks-effects-interactions pattern
- 388     :-        tokenAddress.safeTransfer(_msgSender(), amountRemaining);
+      390:+        tokenAddress.safeTransfer(msgSender_, amountRemaining);
  389, 391:
  390, 392:         // Let withdrawal known to everyone.
- 391     :-        emit Claimed(_msgSender(), amountRemaining);
+      393:+        emit Claimed(msgSender_, amountRemaining);
  392, 394:     }
  393, 395:
  394, 396:     /**
@@ -404,10 +406,11 @@ contract VTVLVesting is Context, AccessProtected {
  404, 406:         // Actually withdraw the tokens
  405, 407:         // Reentrancy note - this operation doesn't touch any of the internal vars, simply transfers
  406, 408:         // Also following Checks-effects-interactions pattern
- 407     :-        tokenAddress.safeTransfer(_msgSender(), _amountRequested);
+      409:+        address msgSender_;
+      410:+        tokenAddress.safeTransfer((msgSender_ = _msgSender()), _amountRequested);
  408, 411:
  409, 412:         // Let the withdrawal known to everyone
- 410     :-        emit AdminWithdrawn(_msgSender(), _amountRequested);
+      413:+        emit AdminWithdrawn(msgSender_, _amountRequested);
  411, 414:     }
  412, 415:
  413, 416:
@@ -423,10 +426,11 @@ contract VTVLVesting is Context, AccessProtected {
  423, 426:
  424, 427:         // No point in revoking something that has been fully consumed
  425, 428:         // so require that there be unconsumed amount
- 426     :-        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
+      429:+        uint112 amountWithdrawn_;
+      430:+        require( (amountWithdrawn_ = _claim.amountWithdrawn) < finalVestAmt, "NO_UNVESTED_AMOUNT");
  427, 431:
  428, 432:         // The amount that is "reclaimed" is equal to the total allocation less what was already withdrawn
- 429     :-        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
+      433:+        uint112 amountRemaining = finalVestAmt - amountWithdrawn_;
  430, 434:
  431, 435:         // Deactivate the claim, and release the appropriate amount of tokens
  432, 436:         _claim.isActive = false;    // This effectively reduces the liability by amountRemaining, so we can reduce the liability numTokensReservedForVesting by that much
```

##### - contracts/token/VariableSupplyERC20Token.sol:[40](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L40)

```
diff --git a/contracts/token/VariableSupplyERC20Token.sol b/contracts/token/VariableSupplyERC20Token.sol
index 1297a14..b0ad5c4 100644
--- a/contracts/token/VariableSupplyERC20Token.sol
+++ b/contracts/token/VariableSupplyERC20Token.sol
@@ -37,10 +37,11 @@ contract VariableSupplyERC20Token is ERC20, AccessProtected {
   37,  37:         require(account != address(0), "INVALID_ADDRESS");
   38,  38:         // If we're using maxSupply, we need to make sure we respect it
   39,  39:         // mintableSupply = 0 means mint at will
-  40     :-        if(mintableSupply > 0) {
-  41     :-            require(amount <= mintableSupply, "INVALID_AMOUNT");
+       40:+        uint256 mintableSupply_;
+       41:+        if((mintableSupply_ = mintableSupply) > 0) {
+       42:+            require(amount <= mintableSupply_, "INVALID_AMOUNT");
   42,  43:             // We need to reduce the amount only if we're using the limit, if not just leave it be
-  43     :-            mintableSupply -= amount;
+       44:+            mintableSupply = mintableSupply_ - amount;
   44,  45:         }
   45,  46:         _mint(account, amount);
   46,  47:     }
```

#### 6. **Using bools for storage incurs overhead (1 instances)**

##### Gas Saved:

Deployements:
**15 697**
Method Calls:
**204**

```
 // Booleans are more expensive than uint256 or any type that takes up a full
 // word because each write operation emits an extra SLOAD to first read the
 // slot's contents, replace the bits taken up by the boolean, and then write
 // back. This is the compiler's defense against contract upgrades and
 // pointer aliasing, and it cannot be disabled.
```

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

##### - contracts/AccessProtected.sol:[12](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L12)

```diff
diff --git a/contracts/AccessProtected.sol b/contracts/AccessProtected.sol
index 188c0dd..2b410a8 100644
--- a/contracts/AccessProtected.sol
+++ b/contracts/AccessProtected.sol
@@ -9,12 +9,12 @@ import "@openzeppelin/contracts/access/Ownable.sol";
    9,   9: @dev Exposes the onlyAdmin modifier, which will revert (ADMIN_ACCESS_REQUIRED) if the caller is not the owner nor the admin.
   10,  10: */
   11,  11: abstract contract AccessProtected is Context {
-  12     :-    mapping(address => bool) private _admins; // user address => admin? mapping
+       12:+    mapping(address => uint256) private _admins; // user address => admin? mapping
   13,  13:
   14,  14:     event AdminAccessSet(address indexed _admin, bool _enabled);
   15,  15:
   16,  16:     constructor() {
-  17     :-        _admins[_msgSender()] = true;
+       17:+        _admins[_msgSender()] = 1;
   18,  18:         emit AdminAccessSet(_msgSender(), true);
   19,  19:     }
   20,  20:
@@ -22,12 +22,12 @@ abstract contract AccessProtected is Context {
   22,  22:      * Throws if called by any account that isn't an admin or an owner.
   23,  23:      */
   24,  24:     modifier onlyAdmin() {
-  25     :-        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
+       25:+        require(_admins[_msgSender()]==1, "ADMIN_ACCESS_REQUIRED");
   26,  26:         _;
   27,  27:     }
   28,  28:
   29,  29:     function isAdmin(address _addressToCheck) external view returns (bool) {
-  30     :-        return _admins[_addressToCheck];
+       30:+        return _admins[_addressToCheck]==1;
   31,  31:     }
   32,  32:
   33,  33:     /**
@@ -38,7 +38,7 @@ abstract contract AccessProtected is Context {
   38,  38:      */
   39,  39:     function setAdmin(address admin, bool isEnabled) public onlyAdmin {
   40,  40:         require(admin != address(0), "INVALID_ADDRESS");
-  41     :-        _admins[admin] = isEnabled;
+       41:+        _admins[admin] = isEnabled?1:0;
   42,  42:         emit AdminAccessSet(admin, isEnabled);
   43,  43:     }
   44,  44: }
\ No newline at end of file
```

#### 7. **It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied (2 instances)**

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address...). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

##### Gas Saved:

Deployements:
**3 995**
Method Calls:
**16**

##### - contracts/VTVLVesting.sol:[27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27), [148](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..cf3d5ad 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -24,7 +24,7 @@ contract VTVLVesting is Context, AccessProtected {
   24,  24:     * In other words, this represents the amount the contract is scheduled to pay out at some point if the
   25,  25:     * owner were to never interact with the contract.
   26,  26:     */
-  27     :-    uint112 public numTokensReservedForVesting = 0;
+       27:+    uint112 public numTokensReservedForVesting;
   28,  28:
   29,  29:     /**
   30,  30:     @notice A structure representing a single claim - supporting linear and cliff vesting.
@@ -145,7 +145,7 @@ contract VTVLVesting is Context, AccessProtected {
  145, 145:     @param _referenceTs Timestamp for which we're calculating
  146, 146:      */
  147, 147:     function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
- 148     :-        uint112 vestAmt = 0;
+      148:+        uint112 vestAmt;
  149, 149:
  150, 150:         // the condition to have anything vested is to be active
  151, 151:         if(_claim.isActive) {
```

#### 8. **Don't compare boolean expressions to boolean literals (1 instance)**

Deployements:
**3 056**
Method Calls:
**48**

##### - contracts/VTVLVesting.sol:[111](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..8f4cbca 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -108,7 +108,7 @@ contract VTVLVesting is Context, AccessProtected {
  108, 108:
  109, 109:         // We however still need the active check, since (due to the name of the function)
  110, 110:         // we want to only allow active claims
- 111     :-        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
+      111:+        require(_claim.isActive, "NO_ACTIVE_CLAIM");
  112, 112:
  113, 113:         // Save gas, omit further checks
  114, 114:         // require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
```

#### 9. **Don't cache a variable that will only be used once (2 instances)**

##### Gas Saved:

Deployements:
**2 388**
Method Calls:
**65**

##### - contracts/VTVLVesting.sol:[124](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L124), [197](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L197)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..7550b92 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -121,12 +121,11 @@ contract VTVLVesting is Context, AccessProtected {
  121, 121:     @dev Requires that all fields are unset
  122, 122:     */
  123, 123:     modifier hasNoClaim(address _recipient) {
- 124     :-        Claim storage _claim = claims[_recipient];
  125, 124:         // Start timestamp != 0 is a sufficient condition for a claim to exist
  126, 125:         // This is because we only ever add claims (or modify startTs) in the createClaim function
  127, 126:         // Which requires that its input startTimestamp be nonzero
  128, 127:         // So therefore, a zero value for this indicates the claim does not exist.
- 129     :-        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
+      128:+        require(claims[_recipient].startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
  130, 129:
  131, 130:         // We don't even need to check for active to be unset, since this function only
  132, 131:         // determines that a claim hasn't been set
@@ -194,8 +193,7 @@ contract VTVLVesting is Context, AccessProtected {
  194, 193:     @dev Simply call the _baseVestedAmount for the claim in question
  195, 194:     */
  196, 195:     function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
- 197     :-        Claim storage _claim = claims[_recipient];
- 198     :-        return _baseVestedAmount(_claim, _referenceTs);
+      196:+        return _baseVestedAmount(claims[_recipient], _referenceTs);
  199, 197:     }
  200, 198:
  201, 199:     /**
```

#### 10. **++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too) (1 instance)**

Saves 6 gas per loop

Pre-increments and pre-decrements are cheaper.

For a uint256 i variable, the following is true with the Optimizer enabled at 10k:

Increment:

i += 1 is the most expensive form
i++ costs 6 gas less than i += 1
++i costs 5 gas less than i++ (11 gas less than i += 1)
Decrement:

i -= 1 is the most expensive form
i-- costs 11 gas less than i -= 1
--i costs 5 gas less than i-- (16 gas less than i -= 1)

##### Gas Saved:

Deployements:
**444**
Method Calls:
**18**

##### - contracts/VTVLVesting.sol:[353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..0d44c1c 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -350,7 +350,7 @@ contract VTVLVesting is Context, AccessProtected {
  350, 350:                 "ARRAY_LENGTH_MISMATCH"
  351, 351:         );
  352, 352:
- 353     :-        for (uint256 i = 0; i < length; i++) {
+      353:+        for (uint256 i = 0; i < length; ++i) {
  354, 354:             _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
  355, 355:         }
  356, 356:
```

#### 11. **`x = x + y` is cheaper than `x += y`** (7 instances)

##### Gas Saved:

Deployements:
**444**
Method Calls:
**42**

##### - contracts/VTVLVesting.sol:[161](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L161), [179](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L179), [301](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L301), [381](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L381), [383](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L383), [433](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L433)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..3f315da 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -158,7 +158,7 @@ contract VTVLVesting is Context, AccessProtected {
  158, 158:             // If we're past the cliffReleaseTimestamp, we release the cliffAmount
  159, 159:             // We don't check here that cliffReleaseTimestamp is after the startTimestamp
  160, 160:             if(_referenceTs >= _claim.cliffReleaseTimestamp) {
- 161     :-                vestAmt += _claim.cliffAmount;
+      161:+                vestAmt = vestAmt + _claim.cliffAmount;
  162, 162:             }
  163, 163:
  164, 164:             // Calculate the linearly vested amount - this is relevant only if we're past the schedule start
@@ -176,7 +176,7 @@ contract VTVLVesting is Context, AccessProtected {
  176, 176:                 uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;
  177, 177:
  178, 178:                 // Having calculated the linearVestAmount, simply add it to the vested amount
- 179     :-                vestAmt += linearVestAmount;
+      179:+                vestAmt = vestAmt + linearVestAmount;
  180, 180:             }
  181, 181:         }
  182, 182:
@@ -298,7 +298,7 @@ contract VTVLVesting is Context, AccessProtected {
  298, 298:
  299, 299:         // Effects limited to lines below
  300, 300:         claims[_recipient] = _claim; // store the claim
- 301     :-        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
+      301:+        numTokensReservedForVesting = numTokensReservedForVesting + allocatedAmount; // track the allocated amount
  302, 302:         vestingRecipients.push(_recipient); // add the vesting recipient to the list
  303, 303:         emit ClaimCreated(_recipient, _claim); // let everyone know
  304, 304:     }
@@ -378,9 +378,9 @@ contract VTVLVesting is Context, AccessProtected {
  378, 378:
  379, 379:         // "Double-entry bookkeeping"
  380, 380:         // Carry out the withdrawal by noting the withdrawn amount, and by transferring the tokens.
- 381     :-        usrClaim.amountWithdrawn += amountRemaining;
+      381:+        usrClaim.amountWithdrawn = usrClaim.amountWithdrawn + amountRemaining;
  382, 382:         // Reduce the allocated amount since the following transaction pays out so the "debt" gets reduced
- 383     :-        numTokensReservedForVesting -= amountRemaining;
+      383:+        numTokensReservedForVesting = numTokensReservedForVesting - amountRemaining;
  384, 384:
  385, 385:         // After the "books" are set, transfer the tokens
  386, 386:         // Reentrancy note - internal vars have been changed by now
@@ -430,7 +430,7 @@ contract VTVLVesting is Context, AccessProtected {
  430, 430:
  431, 431:         // Deactivate the claim, and release the appropriate amount of tokens
  432, 432:         _claim.isActive = false;    // This effectively reduces the liability by amountRemaining, so we can reduce the liability numTokensReservedForVesting by that much
- 433     :-        numTokensReservedForVesting -= amountRemaining; // Reduces the allocation
+      433:+        numTokensReservedForVesting = numTokensReservedForVesting - amountRemaining; // Reduces the allocation
  434, 434:
  435, 435:         // Tell everyone a claim has been revoked.
  436, 436:         emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);
```

##### - contracts/token/VariableSupplyERC20Token.sol:[43](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L43)

```diff
diff --git a/contracts/token/VariableSupplyERC20Token.sol b/contracts/token/VariableSupplyERC20Token.sol
index 1297a14..a39ff7c 100644
--- a/contracts/token/VariableSupplyERC20Token.sol
+++ b/contracts/token/VariableSupplyERC20Token.sol
@@ -40,7 +40,7 @@ contract VariableSupplyERC20Token is ERC20, AccessProtected {
   40,  40:         if(mintableSupply > 0) {
   41,  41:             require(amount <= mintableSupply, "INVALID_AMOUNT");
   42,  42:             // We need to reduce the amount only if we're using the limit, if not just leave it be
-  43     :-            mintableSupply -= amount;
+       43:+            mintableSupply = mintableSupply - amount;
   44,  44:         }
   45,  45:         _mint(account, amount);
   46,  46:     }
```

#### 99. **Overall gas saved**

The result of merging all optimizations

##### Gas Saved:

Deployements:
**660 826**
Method Calls:
**1 849**

```diff
diff --git a/original.txt b/hardhat.txt
index 92050de..a498d8c 100644
--- a/original.txt
+++ b/hardhat.txt
@@ -100,26 +100,26 @@ Compiled 1 Solidity file successfully
 ···················|······················|··············|·············|·············|···············|··············
 |  TestERC20Token  ·  transfer            ·       47587  ·      52339  ·      47809  ·           45  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
-|  VTVLVesting     ·  createClaim         ·      139603  ·     173947  ·     167760  ·           28  ·          -  │
+|  VTVLVesting     ·  createClaim         ·      139360  ·     173704  ·     167517  ·           28  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
-|  VTVLVesting     ·  createClaimsBatch   ·      181558  ·     387422  ·     284486  ·            3  ·          -  │
+|  VTVLVesting     ·  createClaimsBatch   ·      181196  ·     386336  ·     283762  ·            3  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
-|  VTVLVesting     ·  revokeClaim         ·           -  ·          -  ·      40827  ·            6  ·          -  │
+|  VTVLVesting     ·  revokeClaim         ·           -  ·          -  ·      40487  ·            6  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
-|  VTVLVesting     ·  setAdmin            ·       26511  ·      48423  ·      37467  ·            4  ·          -  │
+|  VTVLVesting     ·  setAdmin            ·       26486  ·      48388  ·      37437  ·            4  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
-|  VTVLVesting     ·  withdraw            ·       61171  ·      78271  ·      72938  ·           10  ·          -  │
+|  VTVLVesting     ·  withdraw            ·       60733  ·      77833  ·      72500  ·           10  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
-|  VTVLVesting     ·  withdrawAdmin       ·       43156  ·      64960  ·      54058  ·            4  ·          -  │
+|  VTVLVesting     ·  withdrawAdmin       ·       43087  ·      64891  ·      53989  ·            4  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
-|  VTVLVesting     ·  withdrawOtherToken  ·           -  ·          -  ·      56577  ·            1  ·          -  │
+|  VTVLVesting     ·  withdrawOtherToken  ·           -  ·          -  ·      56571  ·            1  ·          -  │
 ···················|······················|··············|·············|·············|···············|··············
 |  Deployments                            ·                                          ·  % of limit   ·             │
 ··········································|··············|·············|·············|···············|··············
 |  TestERC20Token                         ·           -  ·          -  ·    1193264  ·          4 %  ·          -  │
 ··········································|··············|·············|·············|···············|··············
-|  VTVLVesting                            ·     3740728  ·    3740740  ·    3740739  ·       12.5 %  ·          -  │
+|  VTVLVesting                            ·     3079902  ·    3079914  ·    3079913  ·       10.3 %  ·          -  │
 ·-----------------------------------------|--------------|-------------|-------------|---------------|-------------·

-  63 passing (13s)
+  63 passing (12s)
```
