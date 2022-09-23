## Vulnerability Details

## Low/Non-Critical Findings

### L-01: finalVestedAmount() Can report incorrect or ambiguous values.

The [finalVestedAmount()](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L206-L209) function in VTVLVesting.sol asserts to return the "total vested at the end of the schedule". It does this by using a _claim.endTimestamp value to calculate a claim via the _baseVestedAmount() function.

When a user partially withdraws during the vesting period, the claim's amountWithdrawn values are updated. This change is not reflected in the finalVestedAmount() function, which will report the original final value. The ambiguity comes from interpretations of "total" in the comments versus "final" in the function name.

It may be helpful to rename the function to totalVestedAmount, if this is supposed to reflect the sum total vested throughout the schedule's lifetime. If this should reflect the final amount vested at the end of the schedule, replace [line 208](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L208) with:

```
return _baseVestedAmount(_claim, _claim.endTimestamp) - _claim.amountWithdrawn;
```

There is a possibility that people may be led to believe that an amount remaining vested in a VTVL-produced vesting contract is significantly higher than in reality. The impact of this issue is contextual, therefore I have rated this as Low/QA.

### L-02: Constructor does not check that supplied address is a valid contract.

The constructor in VTVLVesting.sol checks that the supplied _tokenAddress value is not 0. It doesn't check that it's a valid contract.

Check the supplied address' extcodesize to determine whether or not this is a valid contract.

```
uint size;
assembly { size := extcodesize(addr) }
require(size != 0, "NOT_CONTRACT");
```

