## Unrequired check wasting gas

Contract:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107

Issue:
In hasActiveClaim function, there is no need to check require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM"); since it is more than enough to check _claim.isActive==true in hasActiveClaim function

Recommendation:
Modify the hasActiveClaim function to eliminate the below require condition

```
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```