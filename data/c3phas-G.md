## FINDINGS
NB: *Some functions have been truncated where neccessary to just show affected parts of the code*
The gas estimates are the exact results from running the tests included with an exception of internal/public functions  where estimates are based on the known opcodes.
The optimizer is set to run with the default runs(200).
Throught the report some places might be denoted with audit tags to show the actual place affected.

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.


https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L36-L46
### VariableSupplyERC20Token.sol.mint(): mintableSupply should be cached(Saves 2 SLOADs ~200 gas)
```solidity
File: /contracts/token/VariableSupplyERC20Token.sol
36:    function mint(address account, uint256 amount) public onlyAdmin {
37:        require(account != address(0), "INVALID_ADDRESS");
38:        // If we're using maxSupply, we need to make sure we respect it
39:        // mintableSupply = 0 means mint at will
40:        if(mintableSupply > 0) {   //@audit: mintableSupply SLOAD 1
41:            require(amount <= mintableSupply, "INVALID_AMOUNT"); //@audit: mintableSupply SLOAD 2
42:            // We need to reduce the amount only if we're using the limit, if not just leave it be
43:            mintableSupply -= amount; //@audit: mintableSupply SLOAD 3 + 1 SSTORE
44:        }
45:        _mint(account, amount);
46:    }
```

```diff
diff --git a/contracts/token/VariableSupplyERC20Token.sol b/contracts/token/VariableSupplyERC20Token.sol
index 1297a14..7a560c3 100644
--- a/contracts/token/VariableSupplyERC20Token.sol
+++ b/contracts/token/VariableSupplyERC20Token.sol
@@ -37,10 +37,11 @@ contract VariableSupplyERC20Token is ERC20, AccessProtected {
         require(account != address(0), "INVALID_ADDRESS");
         // If we're using maxSupply, we need to make sure we respect it
         // mintableSupply = 0 means mint at will
-        if(mintableSupply > 0) {
-            require(amount <= mintableSupply, "INVALID_AMOUNT");
+        uint256 temp = mintableSupply;
+        if(temp > 0) {
+            require(amount <= temp, "INVALID_AMOUNT");
             // We need to reduce the amount only if we're using the limit, if not just leave it be
-            mintableSupply -= amount;
+            mintableSupply = temp - amount;
         }
         _mint(account, amount);
     }
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L245-L304
### VTVLVesting.sol.\_createClaimUnchecked(): numTokensReservedForVesting should be cached (Saves  ~540 gas, 180 gas for createClaim and 360 gas for createClaimsBatch)

```
Function: createClaim
Average gas before: 163686
Average gas after : 163506

Function:createClaimsBatch
Average gas before: 274860
Average gas after : 274500
```


```solidity
File: /contracts/VTVLVesting.sol
245:    function _createClaimUnchecked(

295:        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE"); //@audit: SLOAD 1

301:        numTokensReservedForVesting += allocatedAmount; // track the allocated amount //@audit: SLOAD 2

    }
```

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..3f30dee 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -290,15 +290,16 @@ contract VTVLVesting is Context, AccessProtected {
         // Our total allocation is simply the full sum of the two amounts, _cliffAmount + _linearVestAmount
         // Not necessary to use the more complex logic from _baseVestedAmount
         uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
+        uint112 temp = numTokensReservedForVesting;

         // Still no effects up to this point (and tokenAddress is selected by contract deployer and is immutable), so no reentrancy risk
-        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
+        require(tokenAddress.balanceOf(address(this)) >= temp + allocatedAmount, "INSUFFICIENT_BALANCE");

         // Done with checks

         // Effects limited to lines below
         claims[_recipient] = _claim; // store the claim
-        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
+        numTokensReservedForVesting = temp + allocatedAmount; // track the allocated amount
         vestingRecipients.push(_recipient); // add the vesting recipient to the list
         emit ClaimCreated(_recipient, _claim); // let everyone know
     }
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L364-L392
### VTVLVesting.sol.withdraw(): usrClaim.amountWithdrawn should be cached(Saves 300 gas) 

```
Average Gas before: 69082
Average Gas After: 68789
```

```solidity
File: /contracts/VTVLVesting.sol
364:    function withdraw() hasActiveClaim(_msgSender()) external {

367:        Claim storage usrClaim = claims[_msgSender()];

374:        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

377:        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;

381:        usrClaim.amountWithdrawn += amountRemaining;

    }
```

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..5b51596 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -365,20 +365,21 @@ contract VTVLVesting is Context, AccessProtected {
         // Get the message sender claim - if any

         Claim storage usrClaim = claims[_msgSender()];
+        uint112 temp = usrClaim.amountWithdrawn;

         // we can use block.timestamp directly here as reference TS, as the function itself will make sure to cap it to endTimestamp
         // Conversion of timestamp to uint40 should be safe since 48 bit allows for a lot of years.
         uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));

         // Make sure we didn't already withdraw more that we're allowed.
-        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
+        require(allowance > temp, "NOTHING_TO_WITHDRAW");

         // Calculate how much can we withdraw (equivalent to the above inequality)
-        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
+        uint112 amountRemaining = allowance - temp;

         // "Double-entry bookkeeping"
         // Carry out the withdrawal by noting the withdrawn amount, and by transferring the tokens.
-        usrClaim.amountWithdrawn += amountRemaining;
+        usrClaim.amountWithdrawn =  temp + amountRemaining;
         // Reduce the allocated amount since the following transaction pays out so the "debt" gets reduced
         numTokensReservedForVesting -= amountRemaining;
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L418-L437
### /VTVLVesting.sol.revokeClaim(): \_claim.amountWithdrawn should be cached(Saves 81 gas)
```
Average Gas before: 36542
Average Gas After: 36451
```

```solidity
File: /contracts/VTVLVesting.sol
418:    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {

420:        Claim storage _claim = claims[_recipient];

426:        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

429:        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;

437:    }
```

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..675a9ab 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -423,10 +423,11 @@ contract VTVLVesting is Context, AccessProtected {

-        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
+        uint112 temp = _claim.amountWithdrawn;
+        require( temp < finalVestAmt, "NO_UNVESTED_AMOUNT");

         // The amount that is "reclaimed" is equal to the total allocation less what was already withdrawn
-        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
+        uint112 amountRemaining = finalVestAmt - temp;

```

## Reorder the require statements to save on gas(Saves ~150 gas , tested on remix)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255-L257
```solidity
File: /contracts/VTVLVesting.sol
255:        require(_recipient != address(0), "INVALID_ADDRESS");
256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

262:        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
264:        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
```

### Explanation based on the first 3 lines for ease of understanding ,see also remix tests based on the same
If `_startTimestamp` is equal to 0, the whole transaction would revert but this would be after evaluating `_linearVestAmount + _cliffAmount > 0` which costs more gas.
If we refactor it, and `_startTimestamp` happens to be 0, then we would fail without consuming the gas for the expression `_linearVestAmount + _cliffAmount > 0`

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..144c228 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -253,8 +253,8 @@ contract VTVLVesting is Context, AccessProtected {
                 ) private  hasNoClaim(_recipient) {

         require(_recipient != address(0), "INVALID_ADDRESS");
-        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
         require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
+        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
```

### Full code diff
```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..891d902 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -253,14 +253,15 @@ contract VTVLVesting is Context, AccessProtected {
                 ) private  hasNoClaim(_recipient) {

         require(_recipient != address(0), "INVALID_ADDRESS");
-        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
         require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
+        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

         require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
-        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
+        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both

         require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
```

### Proof(tested on remix)
```solidity
contract testRequire{

function testReq(address sender, uint40 val,uint40 val2) external pure returns (uint40){
   require (sender != address(0),"INVALID_ADDRESS");
   require(val + val2 > 5,"INVALID_VESTED_AMOUNT");
   require (val > 0, "INVALID_START_TIMESTAMP" );

    return (val);
}
}
```
When val = 0, the execution cost  for the above is : `22411 gas`
```solidity
contract testRequire{

function testReq(address sender, uint40 val,uint40 val2) external pure returns (uint40){
   require (sender != address(0),"INVALID_ADDRESS");
   require (val > 0, "INVALID_START_TIMESTAMP" );
   require(val + val2 > 5,"INVALID_VESTED_AMOUNT");

    return (val);
}
}
```
When val = 0, the execution cost  for the above is : `22293 gas`

### more refactors:
If we split the require statement that uses && we can further reorder them to save more gas.

### Recommendation
Refactor your code to have the require statement that consumes less gas at the top. It saves gas if we fail early as no need to execute complex arithmetics 

## Refactor the code to pack structs elements together
### NB: Only use this if you deem uint32 as a safe option for releaseIntervalSecs
We can pack the struct together by redeclaring the releaseIntervalSecs as a uint32 instead of uint40

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L32-L46
```solidity
File: /contracts/VTVLVesting.sol
    struct Claim {

35:        uint40 startTimestamp; // When does the vesting start (40 bits is enough for TS)
36:        uint40 endTimestamp; // When does the vesting end - the vesting goes linearly between the start and end timestamps
37:        uint40 cliffReleaseTimestamp; // At which timestamp is the cliffAmount released. This must be <= startTimestamp
38:        uint40 releaseIntervalSecs; // Every how many seconds does the vested amount increase. 

42:        uint112 linearVestAmount; // total entitlement
43:        uint112 cliffAmount; // how much is released at the cliff
44:        uint112 amountWithdrawn; // how much was withdrawn thus far - released at the cliffReleaseTimestamp
45:        bool isActive; // whether this claim is active (or revoked)
46:    }    
```

We can modify as follows(Note, the **releaseIntervalSecs** uses **uint32** after modification)

```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..d2b7015 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -32,17 +32,17 @@ contract VTVLVesting is Context, AccessProtected {
     struct Claim {

+        bool isActive; // whether this claim is active (or revoked)
         uint40 startTimestamp; // When does the vesting start (40 bits is enough for TS)
         uint40 endTimestamp; // When does the vesting end - the vesting goes linearly between the start and end timestamps
         uint40 cliffReleaseTimestamp; // At which timestamp is the cliffAmount released. This must be <= startTimestamp
-        uint40 releaseIntervalSecs; // Every how many seconds does the vested amount increase.
-
+        uint112 linearVestAmount; // total entitlement
+
         // uint112 range: range 0 –     5,192,296,858,534,827,628,530,496,329,220,095.
         // uint112 range: range 0 –                             5,192,296,858,534,827.
-        uint112 linearVestAmount; // total entitlement
+        uint32 releaseIntervalSecs; // Every how many seconds does the vested amount increase.
         uint112 cliffAmount; // how much is released at the cliff
         uint112 amountWithdrawn; // how much was withdrawn thus far - released at the cliffReleaseTimestamp
-        bool isActive; // whether this claim is active (or revoked)
     }
```

## Use calldata instead of memory for function parameters
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L333-L341
```solidity
File: /contracts/VTVLVesting.sol
333:    function createClaimsBatch(
334:        address[] memory _recipients, 
335:        uint40[] memory _startTimestamps, 
336:        uint40[] memory _endTimestamps, 
337:        uint40[] memory _cliffReleaseTimestamps, 
338:        uint40[] memory _releaseIntervalsSecs, 
339:        uint112[] memory _linearVestAmounts, 
340:        uint112[] memory _cliffAmounts) 
341:        external onlyAdmin {
```

### x += y costs more gas than x = x + y for state variables
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L43
```solidity
File: /contracts/token/VariableSupplyERC20Token.sol
43:            mintableSupply -= amount;
```

Modify above to:
```solidity
unchecked{
      mintableSupply = mintableSupply - amount;
	}		
```

It's safe to use unchecked blocks here as our operation cannot underflow

**Other instances**

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L301
```solidity
File: /contracts/VTVLVesting.sol
301:        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L381
```solidity
File: /contracts/VTVLVesting.sol
381:        usrClaim.amountWithdrawn += amountRemaining;

```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L383
```solidity
File: contracts/VTVLVesting.sol
383:        numTokensReservedForVesting -= amountRemaining;
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L433
```solidity
File: /contracts/VTVLVesting.sol
433:        numTokensReservedForVesting -= amountRemaining; // Reduces the allocation
```

### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L147
```solidity
File: /contracts/VTVLVesting.sol

//@audit: uint40 _referenceTs
147:    function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {

//@audit:uint112 vestAmt
148:        uint112 vestAmt = 0;

//@audit: uint40 _referenceTs
196:    function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {


//@audit: uint40 _startTimestamp,uint40 _endTimestamp,uint40 _cliffReleaseTimestamp,  uint40 _releaseIntervalSecs,uint112 _linearVestAmount, uint112 _cliffAmount
245:    function _createClaimUnchecked(
246:            address _recipient, 
247:            uint40 _startTimestamp, 
248:            uint40 _endTimestamp, 
249:            uint40 _cliffReleaseTimestamp, 
250:            uint40 _releaseIntervalSecs, 
251:            uint112 _linearVestAmount, 
252:            uint112 _cliffAmount
253:                ) private  hasNoClaim(_recipient) {

//@audit: uint40 _startTimestamp,uint40 _endTimestamp,uint40 _cliffReleaseTimestamp,  uint40 _releaseIntervalSecs,uint112 _linearVestAmount, uint112 _cliffAmount
317:    function createClaim(
318:            address _recipient, 
319:            uint40 _startTimestamp, 
320:            uint40 _endTimestamp, 
321:            uint40 _cliffReleaseTimestamp, 
322:            uint40 _releaseIntervalSecs, 
323:            uint112 _linearVestAmount, 
324:            uint112 _cliffAmount
325:                ) external onlyAdmin {


@audit:uint112 _amountRequested
398:    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    
```

### No need to initialize variables with their default values(wastes gas for state variables)
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). If you explicitly initialize it with its default value, you are just wasting gas.
It costs more gas to initialize variables to zero than to let the default of zero be applied
Declaring uint256 i = 0; means doing an MSTORE of the value 0 Instead you could just declare uint256 i to declare the variable without assigning it’s default value, saving 3 gas per declaration

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27
```solidity
File: /contracts/VTVLVesting.sol
27:    uint112 public numTokensReservedForVesting = 0;
```

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L43
```solidity
File: /contracts/token/VariableSupplyERC20Token.sol
43:            mintableSupply -= amount;
```
The operation `mintableSupply -= amount;` cannot underflow due to the check on [Line 41](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41) that ensures that the value of `mintableSupply` is greater than `amount` before perfoming the operation
Modify above to:

```solidity
unchecked{
      mintableSupply = mintableSupply - amount;
	}		
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L377
```solidity
File: /contracts/VTVLVesting.sol
377:        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
```
The operation `allowance - usrClaim.amountWithdrawn;` cannot underflow due to the check on [Line 374](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374) which ensures that  `allowance` is greater than `usrClaim.amountWithdrawn` before performing the arithmetic operation


https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L429
```solidity
File: /contracts/VTVLVesting.sol
429:        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
```

The operation `finalVestAmt - _claim.amountWithdrawn` cannot underflow due to the check on [Line 426](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L426) that ensures that `finalVestAmt` is greater than `_claim.amountWithdrawn` before performing the arithmetic operation

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L264
```solidity
File: /contracts/VTVLVesting.sol
264:        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
```

The operation `_endTimestamp - _startTimestamp` cannot underflow due to the check on [Line 262](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L262) that ensures that `_endTimestamp` is greater than `_startTimestamp` before performing the arithmetic operation.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L167
```solidity
File: /contracts/VTVLVesting.sol
167:                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
```

The operation `_referenceTs - _claim.startTimestamp` cannot underflow due to the check on [Line 166](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L166) that ensures that `_referenceTs` is greater than `_claim.startTimestamp`


### Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353
```solidity
File: /contracts/VTVLVesting.sol
353:        for (uint256 i = 0; i < length; i++) {
```

### ++i costs less gas compared to i++ or i += 1  (~5 gas per iteration)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:

```solidity
uint i = 1;  
i++; // == 1 but i == 2  
```

But ++i returns the actual incremented value:

```solidity
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

Instances include:

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353
```solidity
File: /contracts/VTVLVesting.sol
353:        for (uint256 i = 0; i < length; i++) {
```

### Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270-L278
```solidity
File: /contracts/VTVLVesting.sol
279:        require( 
280:            (
281:                _cliffReleaseTimestamp > 0 && 
282:                _cliffAmount > 0 && 
283:                _cliffReleaseTimestamp <= _startTimestamp
284:            ) || (
285:                _cliffReleaseTimestamp == 0 && 
286:                _cliffAmount == 0
287:        ), "INVALID_CLIFF");
```


https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L351
```solidity
File: /contracts/VTVLVesting.sol
344:        require(_startTimestamps.length == length &&
345:                _endTimestamps.length == length &&
346:                _cliffReleaseTimestamps.length == length &&
347:                _releaseIntervalsSecs.length == length &&
348:                _linearVestAmounts.length == length &&
349:                _cliffAmounts.length == length, 
350:                "ARRAY_LENGTH_MISMATCH"
351:        );
```
**Proof**
**The following tests were carried out in remix with both optimization turned on and off**

```solidity
function multiple (uint a) public pure returns (uint){
	require ( a > 1 && a < 5, "Initialized");
	return  a + 2;
}
```
**Execution cost**
21617 with optimization and using &&
21976 without optimization and using &&


After splitting the require statement
```solidity
function multiple(uint a) public pure returns (uint){
	require (a > 1 ,"Initialized");
	require (a < 5 , "Initialized");
	return a + 2;
}
```
**Execution cost**
21609 with optimization and split require
21968 without optimization and using split require

### Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.
I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here:

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111
```solidity
File: /contracts/VTVLVesting.sol
111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

### Using bools for storage incurs overhead
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```

See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

**Instances affected include**
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L12
```solidity
File: /contracts/AccessProtected.sol
12:    mapping(address => bool) private _admins; // user address => admin? mapping
```

### Use Custom Errors instead of Revert Strings to save Gas(~50 gas)
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11
```solidity
File: contracts/token/FullPremintERC20Token.sol
11:        require(supply_ > 0, "NO_ZERO_MINT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27
```solidity
File: /contracts/token/VariableSupplyERC20Token.sol
27:        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

37:        require(account != address(0), "INVALID_ADDRESS");

41:            require(amount <= mintableSupply, "INVALID_AMOUNT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L25
```solidity
File: /contracts/AccessProtected.sol
25:         require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");

40:        require(admin != address(0), "INVALID_ADDRESS");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L82
```solidity
File: /contracts/VTVLVesting.sol
82:        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");

107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

129:        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");

255:        require(_recipient != address(0), "INVALID_ADDRESS");
256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

262:        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
264:        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");


295:        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");


374:        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

402:        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");

426:        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

447:        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor

449:        require(bal > 0, "INSUFFICIENT_BALANCE");
```

