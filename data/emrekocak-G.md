## `++i` COSTS LESS GAS COMPARED TO `i++` OR `i += 1`

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:

`uint i = 1; 
i++; // == 1 but i == 2  `
But `++i` returns the actual incremented value:

`uint i = 1; 
++i; // == 2 and i == 2 too, so no need for a temporary variable `
In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`

Instance includes:

[`contracts/VTVLVesting.sol:353`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)
I suggest using ++i instead of i++ to increment the value of a uint variable.
___
## In `require()`, Use `!= 0` Instead of `> 0` With Uint Values

In a require, when checking a uint, using `!= 0` instead of `> 0` saves 6 gas. This will jump over or avoid an extra `ISZERO` opcode.

Instances include:
[`contracts/token/FullPremintERC20Token.sol:11`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11)
[`contracts/token/VariableSupplyERC20Token.sol:27`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27)
[`contracts/VTVLVesting.sol:107`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107)
[`contracts/VTVLVesting.sol:257`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257)
[`contracts/VTVLVesting.sol:263`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263)
[`contracts/VTVLVesting.sol:449`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449)
[`contracts/VTVLVesting.sol:272-273`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272-L273)
___
## Not initializing `unit` variable to default value of zero

Instance includes:
[`contracts/VTVLVesting.sol:353`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)
___
## Use calldata instead of memory for function parameters

It is generally cheaper to load variables directly from calldata, rather than copying them to memory. Only use memory if the variable needs to be modified.

Instances include:
[`contracts/VTVLVesting.sol:333-358`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L333-L358)