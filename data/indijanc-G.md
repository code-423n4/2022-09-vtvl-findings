The contracts are already quite well optimized, but there's still some room for improvements, especially when it comes to `createClaimsBatch()` as it includes a for loop with number of loops based on the input. Here's a few I see.

## Skip comparison to `true` in `hasActiveClaim()` as `isActive` is already a bool variable
[VTVLVesting.sol L111](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111)

`isActive` can be used for the `˙require()` inside `hasActiveClaim()` and the additional comparison to true is unnecessary and just wastes gas.

## Change `i++` to `++i` in for loops
[VTVLVesting.sol L353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)

`++i` will consume less gas because it doesn't need a temporary variable to return current value. Would suggest to use ++i if possible in any loop.

## Use custom errors instead of `require()` with strings
The project is compiled with Solidity v0.8.14 which allows for custom errors (introduced in v0.8.4). Consider using custom errors across all contracts as it will reduce gas cost compared to using `require()` with strings.