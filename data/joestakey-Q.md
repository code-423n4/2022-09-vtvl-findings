# QA Report

## Table of Contents

### Summary

### Low

- [Inconsistent comments for `claims.linearVestAmount`](#inconsistent-comments-for-`claims.linearvestamount`)
- [Logic issue if `Vesting.tokenAddress` is a fee-on-transfer token](#logic-issue-if-`vesting.tokenaddress`-is-a-fee-on-transfer-token)

# Summary

> The code quality is very high. The main issue is an inconsistency with the meaning of a variable in different comments.

# Inconsistent comments for `claims.linearVestAmount`

In `VTVLVesting.sol`, the definition of the struct `Claim` states that `linearVestAmount` is the total entitlement.
But in `createClaim` ( and associated internal functions), `linearVestAmount` is described as:
```
The total amount to be linearly vested between _startTimestamp and _endTimestamp
```

meaning the total entitlement is actually:
```
allocatedAmount = _cliffAmount + _linearVestAmount
```

## Impact

Low


## Tools Used

Manual Analysis



## Mitigation

Change the comment in the struct definition:

```diff
+        uint112 linearVestAmount; // The total amount to be linearly vested between _startTimestamp and _endTimestamp
-        uint112 linearVestAmount; // total entitlement
```


# Logic issue if `Vesting.tokenAddress` is a fee-on-transfer token

If the token used in the vesting contract is a fee-on-transfer token, it affects the expected behavior of the withdrawals functions:

- A user calling `withdraw()` will receive less than the `amountRemaining` that they were expecting to receive.
- The admin calling `withdrawAdmin(_amountRequested)` will receive less than `_amountRequested`.


## Impact

Low


## Tools Used

Manual Analysis

## Mitigation

If you decide to accept fee-on-transfer tokens, add comments to explicitly state that in such case the `amountRemaining` and `_amountRequested` transferred in both withdrawal functions will not match the amount received by the user