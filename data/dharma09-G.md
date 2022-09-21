  ## 1.Require statements including conditions with the `&&` operator can be broken down in multiple require statements to save gas.

## PROOF OF CONCEPT
Instances include:
[VTVLVesting.sol#L344](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344)

Change :    
require(_startTimestamps.length == length ,"ARRAY_LENGTH_MISMATCH");
require( _endTimestamps.length == length ,"ARRAY_LENGTH_MISMATCH");
require( _cliffReleaseTimestamps.length == length  ,"ARRAY_LENGTH_MISMATCH");
require(_releaseIntervalsSecs.length == length ,"ARRAY_LENGTH_MISMATCH");
require(_linearVestAmounts.length == length ,"ARRAY_LENGTH_MISMATCH");
require(_cliffAmounts.length == length,  "ARRAY_LENGTH_MISMATCH");

[VTVLVesting.sol#L270](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270)

require((_cliffReleaseTimestamp >= 0 , "INVALID_CLIFF");
require( _cliffAmount >= 0 , "INVALID_CLIFF");
require(_cliffReleaseTimestamp <= _startTimestamp, "INVALID_CLIFF");
   


## 2.DEFAULT VALUE INITIALIZATION

### PROBLEM
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### PROOF OF CONCEPT
Instances include:
[VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)
