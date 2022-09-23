### 1. calldata for parameters instead of memory in createClaimsBatch

It is reasonable to use `calldata` instead of `memory` for input arrays in `createClaimsBatch` function (which is `external`) to reduce gas consumption and make the code more clear.

### 2. redundant check in hasActiveClaim modifier

There is the following check in the `hasActiveClaim` modifier:

```solidity
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```

It is redundant statement, because below there is the following check:

```solidity
require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

In case the second check do not revert the first one will also pass.

### 3. hashing the Claim struct

It is reasonable to hash the Claim structs and store only the hash instead of storing all of them in the storage. Later users will be able to prove that provided struct data corresponds to the real one by checking the corresponding hash. This will substantially reduce gas consumption.