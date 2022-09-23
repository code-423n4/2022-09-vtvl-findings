## Use custom errors rather than `revert()`/`require()` strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save [**\~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hitby [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*Identified instances:*

```solidity
File: /contracts/VTVLVesting.sol

82:         require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");

107:        require(_claim.startTimestamp != 0, "NO_ACTIVE_CLAIM"); 

111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

255:        require(_recipient != address(0), "INVALID_ADDRESS");
256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

262:        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); 
263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
264:        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

270:        require( 
271:            (
272:                _cliffReleaseTimestamp > 0 && 
273:                _cliffAmount > 0 && 
274:                _cliffReleaseTimestamp <= _startTimestamp
275:            ) || (
276:                _cliffReleaseTimestamp == 0 && 
277:                _cliffAmount == 0
278:        ), "INVALID_CLIFF");

295:        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

374:        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

402:        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");

426:        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT"); 

447:        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor

449:        require(bal > 0, "INSUFFICIENT_BALANCE");

```


```solidity
File: /contracts/AccessProtected.sol

23:     require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
40:     require(admin != address(0), "INVALID_ADDRESS");
```

## Boolean comparisons

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value. Consider using the direct value.

*Identified instances:*

```solidity
File: /contracts/VTVLVesting.

111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

## ++i costs less gas compared to i++ or i += 1 in for loops (\~5 gas per iteration)

\++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:

    uint i = 1;  
    i++; // == 1 but i == 2  

But ++i returns the actual incremented value:

    uint i = 1;  
    ++i; // == 2 and i == 2 too, so no need for a temporary variable  

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

Instances include:

```solidity
File: /contracts/VTVLVesting.

353:    for (uint256 i = 0; i < length; i++) {
354:        _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
355:    }
```

## Using unchecked blocks to save gas - Increments in for loop can be unchecked

The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```
for(uint256 i; i < 10; i++){
//doSomething
}

```

can be written as shown below.

    for(uint256 i; i < 10;) {
      // loop logic
      unchecked { i++; }
    }

We can also write it as an inlined function like below.

    function inc(i) internal pure returns (uint256) {
      unchecked { return i + 1; }
    }
    for(uint256 i; i < 10; i = inc(i)) {
      // doSomething
    }

Instances include:
```solidity
File: /contracts/VTVLVesting.

353:    for (uint256 i = 0; i < length; i++) {
354:        _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
355:    }
```

## Avoid calling balanceOf when creating batch claims

At [VTVLVesting.sol#L295](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295) there's an external call to check the balance for a given address. When creating batch claims, the value should be the same, so the external call should be avoided. 

Consider some code refactoring in order to get the value a single time. Simple implementation results in the following bennefit:
```
original   |  VTVLVesting     ·  createClaimsBatch   ·      181546  ·     387410  ·     284474  ·            3  ·          -  │
refactored |  VTVLVesting     ·  createClaimsBatch   ·      181576  ·     384336  ·     282952  ·            3  ·          -  │
```