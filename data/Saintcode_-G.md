## USE CALLDATA INSTEAD OF MEMORY

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obviates the need for such a loop in the contract code and runtime execution.

When arguments are read-only on external functions, the data location should be `calldata`

8 Instances:
-VTVLVesting.sol lines 147, 334, 335, 336, 337, 338, 339, 340


## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

2 Instances:
-VTVLVesting.sol lines 301, 381


## ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS REQUIRE() OR IF-STATEMENT

5 Instances:
-VTVLVesting.sol lines 167, 170, 264, 337, 429, 

## `++I` INSTEAD OF `I++` (OR USE ASSEMBLY WHEN APPLICABLE)

Use `++i` instead of ` i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. 

1 Instance:
-VTVLVesting.sol line 353 

## USE MULTIPLE `REQUIRE()` STATMENTS INSTEAD OF `REQUIRE(EXPRESSION && EXPRESSION && ...)`

1 Instance:
-VTVLVesting.sol lines 344

## IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {

3 Instances:
-VTVLVesting.sol lines 27, 148, 353


## `>=` COSTS LESS GAS THAN `>`

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas
Recommended fix example: change `variable > 0` to `variable >= 1`

9 Instances:
-VTVLVesting.sol lines 107, 257, 263, 272, 273
-FullPremintERC20Token.sol line 11
-VariableSupplyERC20Token.sol line 27, 31, 40

## `++I/I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
`
   for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
           // Do the thing
           // Unchecked pre-increment is cheapest
           unchecked { ++i; }   
}  `

1 Instance:
-VTVLVesting.sol line 353






## USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

24 Instances:
-AccessProtected.sol lines 25, 40
-VTVLVested.sol 82, 107, 111, 129, 255, 256, 257, 262, 263, 264, 270, 295, 344, 374, 402, 426, 447, 449
-FullPremintERC20Token.sol line 11
-VariableSupplyERC20Token.sol lines 27, 37, 41

