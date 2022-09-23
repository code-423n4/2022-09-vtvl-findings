### `require` statements with `and` conditionals should be separated into different `require` statements, consuming less gas.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L351

### Consider using custom errors, as they consume less gas than `require()` statements.

There are 17 instances in [VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol) and 2 in  [AccessProtected.sol](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol)