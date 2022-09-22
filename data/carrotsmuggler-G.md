# Gas

## 1. `VTVLVesting.sol`:353: ++i is more efficient than i++

i++ increments i and returns the initial value of i, but ++i returns the actual incremented value skipping the creation of a temporary variable, saving ~5 gas/iteration.

## 2. `VTVLVesting.sol`:374,377,381 usrClaim.amountWithdrawn should be cached in memory

```solidity
require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
//
uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
//
usrClaim.amountWithdrawn += amountRemaining;
```

Multiple use of storage variable costs 100 gas to load from warm state each time. Caching in memory makes future calls cost 3 gas, saving ~97 gas each call.
Caching here changes average gas cost from 69082 to 68740 (hardhat gas reporter average)

Use cached value for increment as:

```solidity
numTokensReservedForVesting = cached + allocatedAmount;
```

## 3. `VTVLVesting.sol`:426,429 usrClaim.amountWithdrawn should be cached in memory

```solidity
require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
//
uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
```

Multiple use of storage variable costs 100 gas to load from warm state each time. Caching in memory makes future calls cost 3 gas, saving ~97 gas each call.
Caching here changes average gas cost from 36548 to 36451 (hardhat gas reporter average)

## 4. `VTVLVesting.sol`:295,301 numTokensReservedForVesting should be cached in memory

```solidity
require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
//
numTokensReservedForVesting += allocatedAmount; // track the allocated amount
```

Multiple use of storage variable costs 100 gas to load from warm state each time. Caching in memory makes future calls cost 3 gas, saving ~97 gas each call.
Caching here changes average gas cost from 36548 to 36451 (hardhat gas reporter average)

Use cached value for increment as:

```solidity
numTokensReservedForVesting = cached + allocatedAmount;
```

## 5. `VTVLVesting.sol`:166,167,170 startTimestamp should be cached in memory

```solidity
if(_referenceTs > _claim.startTimestamp) {
//
uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
//
uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval
```

Multiple use of storage variable costs 100 gas to load from warm state each time. Caching in memory makes future calls cost 3 gas, saving ~97 gas each call.
Caching here changes average gas cost from 36548 to 36451 (hardhat gas reporter average)
