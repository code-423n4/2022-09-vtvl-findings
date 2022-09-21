1.	Cache `mintableSupply` to memory variable and in the end save that variable back to `mintableSupply` to save gas. This way you will call `sload` 1 time instead of 2.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40-L44
2.	Do not initialize variable with default values
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148
3.	Use `++i` instead of `i++` to save gas, especially in loops.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353 
