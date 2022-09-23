## `cliffReleaseTimestamp` as part of the `Claim` struct is unnecessary.

When a `claim` is created the `cliffReleaseTimestamp` is [required](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L274) to be `<= startTimestamp`. 

As the claim isn't valid until after the `startTimestamp` this means they are in fact equal except for the case where `cliffReleaseTimestamp` is `0` which is used to indicate there is no cliff release, but this can also be done with the `cliffAmount`.

As a result the `cliffReleaseTimestamp` can be removed from the struct and the code simplified.
Unless of course a cliff release after the start timestamp is a valid use case (in which case this is should be a higher risk rating),  but I presume this is not the case as the [comment](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L269) explicitly affirms this.