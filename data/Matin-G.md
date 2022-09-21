1 - No need to initialize a variable to its default value: (default value of uint256 is 0)
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

2 - Pre-incrementing lowers the gas cost in a for loop: (++i instead of traditional i++)
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

3 - Arrays inside the function arguments should be declared as call data instead of memory:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L333-L340