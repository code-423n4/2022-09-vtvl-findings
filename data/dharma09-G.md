  ## 1.Require statements including conditions with the `&&` operator can be broken down in multiple require statements to save gas.

## PROOF OF CONCEPT
Instances include:
[VTVLVesting.sol#L344](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344)

Change :    
```
require(_startTimestamps.length == length ,"ARRAY_LENGTH_MISMATCH");
require( _endTimestamps.length == length ,"ARRAY_LENGTH_MISMATCH");
require( _cliffReleaseTimestamps.length == length  ,"ARRAY_LENGTH_MISMATCH");
require(_releaseIntervalsSecs.length == length ,"ARRAY_LENGTH_MISMATCH");
require(_linearVestAmounts.length == length ,"ARRAY_LENGTH_MISMATCH");
require(_cliffAmounts.length == length,  "ARRAY_LENGTH_MISMATCH");
```

[VTVLVesting.sol#L270](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270)

I suggest changing > 0 with != 0 here:

```
require((_cliffReleaseTimestamp =! 0 , "INVALID_CLIFF");
require( _cliffAmount =! 0 , "INVALID_CLIFF");
require(_cliffReleaseTimestamp <= _startTimestamp, "INVALID_CLIFF");
```
   
## 2.DEFAULT VALUE INITIALIZATION

### PROBLEM
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### PROOF OF CONCEPT
Instances include:
[VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)

AND we can use unchecked which can save Gas For above instances:
```
 for (uint256 i; i < length; ) {
         _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[I]);
unchecked {
	    ++i;
           }
        }
```

## 3.COMPARISONS: != IS MORE EFFICIENT THAN > IN REQUIRE (6 GAS LESS)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)

For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check > 0 is essentially checking that the value is not equal to 0 therefore >0 can be replaced with !=0 which saves gas.

While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas.

## PROOF OF CONCEPT
Instances include:
[VTVLVesting.sol#L449](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449)
[VTVLVesting.sol#L256](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256)
[VTVLVesting.sol#L257](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257)
[VTVLVesting.sol#L263](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263)

[VariableSupplyERC20Token.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27)

## 4.USING PRIVATE RATHER THAN PUBLIC FOR `immutable`, SAVES GAS

Instances:
[VTVLVesting.sol#L17](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L17)
```
IERC20 public immutable tokenAddress;
```