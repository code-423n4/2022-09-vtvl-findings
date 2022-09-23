 # 1 Use != 0 instead of > 0 at the above mentioned codes. The variable is uint, so it will not be below 0 so it can just check != 0.
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40
# 2 #  ( ++I/I++ ) SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
++I COSTS LESS GAS THAN I++
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
For example
for (uint i; i < length; ) {
    // code
    unchecked { ++i; }
} 
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353
# 3 USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.
If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it’s still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one .
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.
Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified. 
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L91
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L223
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L334
# 4  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L161
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L179
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381
# 5  USE MULTIPLE REQUIRE() STATMENTS INSTED OF REQUIRE(EXPRESSION && EXPRESSION && ...)
SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344