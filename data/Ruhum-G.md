# Gas Report

- [G-01: remove unnecessary require statements that can be caught by overflows](#g-01-remove-unnecessary-require-statements-that-can-be-caught-by-overflows)
- [G-02: only compute vested amount if the claim's `linearVestAmount` is not 0](#g-02-only-compute-vested-amount-if-the-claims-linearvestamount-is-not-0)
- [G-03: `finalVestedAmount()` doesn't need to compute vested amount](#g-03-finalvestedamount-doesnt-need-to-compute-vested-amount)
- [G-04: `hasActiveClaim()` doesn't need to check claim's startTimestamp](#g-04-hasactiveclaim-doesnt-need-to-check-claims-starttimestamp)
- [G-05: `hasActiveClaim()` modifier unnecessary for `withdraw()`](#g-05-hasactiveclaim-modifier-unnecessary-for-withdraw)
- [G-06: working with uints smaller than 256 is more expensive](#g-06-working-with-uints-smaller-than-256-is-more-expensive)

## G-01: remove unnecessary require statements that can be caught by overflows

If you're going to do `A - B`, there's no need to check whether `A > B` in a prior statement. The `A - B` will simply underflow and revert. It only makes sense if you want to include a detailed error message. In that case, it makes sense to put the `A - B` statement into an `unchecked` block to prevent the underlying underflow check. Both options will save gas. It's simply a matter of taste:

- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L374-L377
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426-L429

## G-02: only compute vested amount if the claim's `linearVestAmount` is not 0

Since `linearVestAmount` can be 0, the whole computation of the vested amount will also be 0 in that case. The computation is done with every call unless it's called before the vesting period starts. User's who don't have any vested amount but just the `cliffAmount` can save a lot of gas if you don't execute the vesting logic:

```sol
function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
    // ... rest of logic
        if(_referenceTs > _claim.startTimestamp && _claim.linearVestAmount != 0) {
            // ... rest of logic
        }
    }
}
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L166

## G-03: `finalVestedAmount()` doesn't need to compute vested amount

You already know what the final vested amount will be: `_claim.linearVestAmount + _claim.cliffAmount`. Just return that instead of computing it using `_baseVestedAmount()`

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L208

## G-04: `hasActiveClaim()` doesn't need to check claim's startTimestamp

You already check whether `isActive` is `true`. That's enough to know whether there's an active claim or not. Your reason to check for `startTimestamp` is: 
> [...] we only ever set startTimestamp in createClaim, and we never change it. Therefore, startTimestamp will be set IFF a claim has been created.

That's the same for `isActive`. The property is only set to `true` when creating a claim.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107

## G-05: `hasActiveClaim()` modifier unnecessary for `withdraw()`

Inside the `withdraw()` function, you call `vestedAmount()`. Inside there, you already check whether the user's claim is active. If it's not, the function returns `0` or `amountWithdrawn`. With either value, the `withdraw()` function will revert because of the `require` statement or the resulting underflow here.

```
function withdraw() hasActiveClaim(_msgSender()) external {
    // three scenarios:
    // - no claim at all (not initialized)
    // - inactive claim
    // - active claim (can ignore this scenario because that's the modifier allows anyways)
    Claim storage usrClaim = claims[_msgSender()];
    
    // if there's no claim, this will return `0` because all the underlying values are `0`.
    // If the claim is inactive, it will return either `0` or `claim.amountWithdrawn`, see `_baseVestedAmount()` function
    uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));

    // Either way, the maximum value that `allowance` has is `usrClaim.amountWithdrawn` when there's no claim or it's inactive.
    // This check will fail and the function will revert.
    require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

    // ... rest of logic
}
```

Removing the modifier will save you the SLOAD where you check the claim's timestamp/isActive status.

## G-06: working with uints smaller than 256 is more expensive

While packing structs can save gas, it's more expensive to work with uints smaller than 256. The EVM works with 32 byte values. If the value is smaller than that, it has to adjust the size of the element to operate on it. That costs gas. 

For example, in the `_baseVestedAmount()` function you work with uints that are all smaller than 256 bits. Doing the same math with `uint256` and converting to the smaller type at the end should save you gas:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L147-L187