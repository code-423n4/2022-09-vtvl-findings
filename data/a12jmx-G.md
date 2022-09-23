Contract: VTVLVesting.sol

Changing i++ to ++i in the for loop of:

	line 353

	will save around 5 gas per iteration.
	
Recommendation:

	for (uint256 i = 0; i < length; ++i) {