### [N-01] `hasActiveClaim` modifier has an unnecessary check

The unnecessary check is here:

```solidity=
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107

The condition `_claim.isActive == true` is enough, as a storage slot will be all set to 0 if the claim was never created (i.e. a claim that was not created cannot be active).


