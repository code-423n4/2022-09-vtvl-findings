### 1. allVestingRecipients function fail

As we see `allVestingRecipients` function returns all the `vestingRecipients` array. Especially, the `vestingRecipients` array will be read from storage and later copied into memory. In the case of big enought number of the elements of this array this function will use big amount of gas, as it uses at least 2100 gas for reading corresponding storage slot for each recipient (actually it can use more gas that can fit into block gas limit in case when there is at least ~1500 recipients, because 1500*2100 > 30M).

It is reasonable to create an external view function that gives ability to read only part of that array.

### 2. ERC20 with permit

It is considered good practice to use ERC20 standard [modification](https://docs.openzeppelin.com/contracts/4.x/api/token/erc20#ERC20Permit) that contains `permit` function.

### 3. wrong hasNoClaim modifier description

`hasNoClaim` modifier's description states `"@notice Modifier which is opposite hasActiveClaim"`. But actually it is not the opposite for the `hasActiveClaim` modifier, because `hasActiveClaim` also checks that recipient's claim is active but not that recipient have any claim.

### 4. wrong NOTHING_TO_WITHDRAW require description

There is the following check in the `withdraw` function.

```solidity
// Make sure we didn't already withdraw more that we're allowed.
require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
```

But the description above the `require` statement is wrong --- the right one is `"Make sure we didn't already withdraw more or equal to what we're allowed.`.

### 5. redundant check in hasActiveClaim modifier

There is the following check in the `hasActiveClaim` modifier:

```solidity
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```

It is redundant statement, because below there is the following check:

```solidity
require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

In case the second check do not revert the first one will also pass.