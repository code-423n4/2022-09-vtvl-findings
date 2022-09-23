# Use != 0 instead of > 0 for Unsigned Integer Comparison
When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40
#  ( ++I/I++ ) SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN 2  FOR- AND WHILE-LOOPS
++I COSTS LESS GAS THAN I++
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353