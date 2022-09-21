# [G-01] hasActiveClaim: modifier should be changed to function

## Descriprion

The `hasActiveClaim` modifier takes `recipient`'s address and reads the corresponding `Claim` from the storage. This modifier is used along with `withdraw()` and `revokeClaim()` functions, which read the same `Claim` from the storage once again.

## Recommendations

1. Transform the `hasActiveClaim` modifier into a function:

```
function hasActiveClaim(Claim memory _claim) internal view returns (bool) {
    return _claim.startTimestamp > 0 && _claim.isActive;
}
```

2. Read the `Claim` from the storage first and then feed it into the `hasActiveClaim` function in the following way:

```
function withdraw() external {
    // Get the message sender claim - if any

    Claim storage usrClaim = claims[_msgSender()];

    require(hasActiveClaim(usrClaim), "NO_ACTIVE_CLAIM");

    ......
}
```

```
function revokeClaim(address _recipient) external onlyAdmin {
    // Fetch the claim
    Claim storage _claim = claims[_recipient];

    require(hasActiveClaim(_claim), "NO_ACTIVE_CLAIM");

    ....
}
```

# [G-02] withdraw: change vestedAmount to \_baseVestedAmount

## Description

While calling `withdraw` function a user's allowance is determined by the `vestedAmount` function, which in turn reads the user's `Claim` from the storage. However, this is unnecessary, as the `Claim` has been already read from the storage (L367).

## Recommendations

Change L371 to

```
    uint112 allowance = _baseVestedAmount(usrClaim, uint40(block.timestamp));
```

# [G-03] withdraw: change vestedAmount to \_baseVestedAmount

## Description

While calling `revokeClaim` function total amount of tokens which a recipient is entitled to is determined by the `finalVestedAmount` function, which in turn reads the recipient's `Claim` from the storage. However, this is unnecessary, as the `Claim` has been already read from the storage (L367).

## Recommendations

Change L422 to

```
    uint112 finalVestAmt = _baseVestedAmount(_claim, _claim.endTimestamp);
```

# Unchecked math

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block: https://docs.soliditylang.org/en/v0.8.7/control-structures.html#checked-or-unchecked-arithmetic

## [G-04] \_baseVestedAmount: L167 should be uncheked due to L166

## [G-05] \_baseVestedAmount: L170 should be uncheked due to L262

## [G-06] withdraw: L377 should be unchecked due to L374

## [G-07] revokeClaim: L429 should be unchecked due to L426

# [G-08] all unsigned integer operations: > 0 is less efficient than != 0 for unsigned integers

!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)
