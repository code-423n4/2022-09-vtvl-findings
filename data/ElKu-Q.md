# Non-critical Issues

## 1. `createClaimsBatch` function could be modified to avoid unexpected reverts

Right now, the [createClaimsBatch](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L333) function repeatedly calls the [_createClaimUnchecked](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L245) internal function. Which in turn contains many require statements before a `claim` is created. If any one of these claim creation's revert, then the whole `createClaimsBatch` function would revert wasting a significant amount of gas.

I recommend that a `try and catch` block approach should be used instead. The `try` block could try creating the claim with the `_createClaimUnchecked` function and if it reverts, we can add the particular recipient to an address array in the `catch` block. This list of failed recipient addresses could be returned to the user, which he can check and make necessary corrections so that he can call the batch function again.

## 2. Missing emit statement in the `withdrawOtherToken` function
[withdrawOtherToken](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L446) function should have an emit statement to let the user base know that the admin has withdrawn the `other tokens` which were wrongly sent to the contract.

## 3. The emit statement should show which particular `admin` called the function
The following functions doesn't emit information on which admin called the method. I suggest this information to be emitted as well.
 1. [_createClaimUnchecked](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L303)
 2. [revokeClaim](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L436)

