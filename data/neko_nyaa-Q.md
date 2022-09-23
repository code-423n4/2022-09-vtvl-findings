### [N-01] `hasActiveClaim` modifier has an unnecessary check

The unnecessary check is here:

```solidity=
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107

The condition `_claim.isActive == true` is enough, as a storage slot will be all set to 0 if the claim was never created (i.e. a claim that was not created cannot be active).

### [N-02] `withdrawAdmin()` should be made `external` rather than `public`

For it is not called anywhere within the contract. Also on the docs it is external

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398
https://github.com/code-423n4/2022-09-vtvl/blob/main/docs/VTVLVesting.md#withdrawadmin
