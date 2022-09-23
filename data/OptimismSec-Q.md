# QA Report

## Remarks/Recommendations

It would probably be a good idea for there to be a contract owner that can never be removed and has more privileges than the admins, so that functionality can be restored if it's deemed necessary.
  
## Coding style improvements

1. 

[VTVLVesting.sol:L111](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111)

```solidity
require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

The `== true` is redundant

2. There should be a `remove` or `unsetAdmin` function for more code clarity (using the same name `setAdmin` to remove admins)

[AccessProtected.sol#L39](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39)

## Documentation improvements

1. On [line 370](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L370)

```solidity
// Conversion of timestamp to uint40 should be safe since 48 bit allows for a lot of years.
```

should be changed to `since 40 bits allows for`

2. 

More documentation is required for the "zero admin" case. Discussion with the sponsor confirmed that the case of their being zero admins is supported. However, there should be more documentation provided for this feature. In particular users should be informed that they will not be able to call many of the functions of the contract should all admins be removed.
