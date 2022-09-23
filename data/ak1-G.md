1. https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L206
   To get the `finalVestedAmount`, no need to call `_baseVestedAmount`.
    The `finalVestedAmount`, can be calculated by directly adding the `_claim.cliffAmount+_claim.linearVestAmount`

2. In `_baseVestedAmount` calculation, at the start of the function, add check if the `_referenceTs <= start time of the cliam` . Can save some amount of gas that could be consumed by calculation.