# Array Optimization
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353-L355
```i++``` can become ```++i``` to save approximately 5 gas per iteration
The part ```uint i = 0``` can become ```uint i``` as just letting the default be applied is cheaper than setting ```i``` to the default value of 0. 
The ```++i``` part can also become unchecked, ```unchecked{ ++i; }``` as the ```createClaimsBatch``` function will certainly run out of gas before ```++i``` overflows from getting to uint256(-1). 

#### Therefore the new array will be:
```
for (uint256 i; i < length; ) { 
    _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], 
    _linearVestAmounts[i], _cliffAmounts[i]); 
    unchecked{ ++i; }
}
```


# Using bools for storage incurs unnecessary overhead
Using ```uint256(1)```and ```uint256(2)``` for true/false avoids a 100 gas Gwarmaccess, and avoids a 20000 gas Gsset when changing from false to true after having been a true value in the past.

Instances are:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L45
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L12

 
# Letting the default of zero be applied to uint variables saves gas
Setting a uint variable to zero costs more than allowing the default value of zero be applied, therefore the ```= 0``` can be removed in these instances:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148
(I already mentioned in the array optimization section)  https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353
