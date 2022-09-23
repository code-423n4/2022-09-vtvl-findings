# Gas Savings

## Duplicate function in VTVLVesting
In VTVLVesting, the function `finalVestedAmount()` is duplicative of `vestedAmount()` and can be removed to save on deployment costs.

This will require making a change in `revokeClaim` to call `vestedAmount(recipient, _claim.endTimestamp)` instead of `finalVestedAmount`.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L206

## Use != 0 instead of > 0 for Unsigned Integer Comparison
When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

This method can be used in VTVLVesting, FullPremintERC20Token and VariableSupplyERC20Token.

## `++I` costs less gas compared to `I++` or `I += 1`
`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

Consider changing this in `VTVLVesting.createClaimsBatch()` to 
```javascript
for (uint256 i = 0; i < length; ++i) { // changed i++ to ++i

_createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);

}
```

## INCREMENTS CAN BE UNCHECKED
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

Consider changing this in `VTVLVesting.createClaimsBatch()` to 
```javascript
for (uint256 i = 0; i < length;) { // removed ++i

_createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
unchecked {++i;}
}
```

## Modifier `hasNoClaim` reads from storage
The modifier `VTVLVesting.hasNoClaim()` loads a recipient's `claim` from `storage` and determines they do not have a claim if `startTimestamp == 0`. 

This can be done for less gas by checking to see if the `recipient` address is present in `vestingRecipients` array since `vestingRecipients` is only set if the `recipient` has a claim.

## Require statement can be removed to save gas
In `VTVLVesting.withdraw()` the require statement on L374 can be safely removed since the following L377 will cause a revert if `allowance` is not greater than or equal to `userClaim.amountWithdrawn` since the subtraction will cause an underflow in Solidity's checked math.

```javascript
require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");` 
uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374

## Use `external` instead of `public` where possible
Functions with the `public` visibility modifier are costlier than `external`. Default to using the `external` modifier until you need to expose it to other functions within your contract.

Consider changing the visibility of these functions in to `external`

In VTVLVesting
- withdrawAdmin

## Don't Initialize Variables with Default Value
Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

Consider removing variables initialized to their default values in VTVLVesting.
- numTokensReservedForVesting

## Use custom errors
Consider replacing revert strings with custom errors. [Custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met.)

This can be implemented in contracts AccessProtected, VTVLVesting, FullPremintERC20Token and VariableSupplyERC20Token.
