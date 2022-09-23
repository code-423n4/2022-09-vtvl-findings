[G-01] USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS

Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. You could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.
Source: https://blog.soliditylang.org/2021/04/21/custom-errors/

Additional recommendation:
We can add natspec documentation to explain the custom errors in contract.

Instances:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L37
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L41
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L25
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L40
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L82
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L129
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L255
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L262
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L264
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L278

[G-02] CACHE STORAGE VALUES IN MEMORY TO MINIMIZE SLOADS
The code can be optimized by minimizing the number of SLOADs. SLOADs are expensive 100 gas compared to MLOADs/MSTOREs (3gas)
Storage value should get cached in memory

Instance:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L11

Being read two times:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43

[G-03] ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 IN FOR LOOPS (~5 GAS PER ITERATION)

Pre-increments are cheaper than post-increments.

For a uint256 i variable, pre-increment ++i costs 5 gas  less than i++ with the optimizer enabled.

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name post-increment:
However, pre-increments (or pre-decrements) return the new value.

Instance:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

[G-04]  USING UNCHECKED FOR INCREMENTS TO SAVE GAS

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration but at the cost of some code readability, as this uncheck cannot be made inline.
Source: https://github.com/ethereum/solidity/issues/10695

Instance
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

Sample affected code at file VTVLVesting.sol#L353:

for (uint256 i = 0; i < length; i++) {
                _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], 
                        _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
               );
}

can be changed to the following:

for (uint256 i = 0; i < length;) {
                _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], 
                    _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
               );
           unchecked {
                        ++i;
                }
       }

[G-05] IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

In Solidity, all variables are set to zeros by default. So, do not explicitly initialize a variable with its default value if it is zero.
As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {

Instances:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27

[G-06] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS - (SAVES 8 GAS PER &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&.

The gas difference would only be realized if the revert condition is realized(met).

Instances:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L348
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272-L276
