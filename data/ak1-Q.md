1. For safer side, type cast the time variable into `uint112` during following math operation.
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L176
updated code :
`uint112 linearVestAmount = _claim.linearVestAmount * uint112(truncatedCurrentVestingDurationSecs) / uint112(finalVestingDurationSecs)`