# Breaking down multiple `require` statements instead of using `&&`

## Details
Require statements including conditions with the && operator can be broken down in multiple require statements to save gas.
See reference: [G-09] of https://code4rena.com/reports/2022-04-backd/

## Mitigation
I suggest breaking down six conditions into six `require` statement instead of using `&&`. Example:
Changing from:
```
require(_startTimestamps.length == length &&
        _endTimestamps.length == length &&
        _cliffReleaseTimestamps.length == length &&
        _releaseIntervalsSecs.length == length &&
        _linearVestAmounts.length == length &&
        _cliffAmounts.length == length, 
        "ARRAY_LENGTH_MISMATCH"
        );
```
to:
```
require(_startTimestamps.length == length,"ARRAY_LENGTH_MISMATCH");
require(_endTimestamps.length == length,"ARRAY_LENGTH_MISMATCH");
require(_cliffReleaseTimestamps.length == length,"ARRAY_LENGTH_MISMATCH");
require(_releaseIntervalsSecs.length == length,"ARRAY_LENGTH_MISMATCH");
require(_linearVestAmounts.length == length,"ARRAY_LENGTH_MISMATCH");
require(_cliffAmounts.length == length,"ARRAY_LENGTH_MISMATCH");

```

## Line of code
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L351

___
# Pre-increment cost less gas than post-increment

## Details
`i++` costs more gas than `++i` , for uint pre-decrement is cheaper than post-decrement
see reference: https://github.com/code-423n4/2021-12-nftx-findings/issues/195

## Mitigation
change `i++` to `++i`

## Line of code
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353

___
# Functions that are not called within the contract must set its visibility to `external` instead of `public`

## Details
Setting function's visibility to external when it is only called externally can save gas because external function’s parameters are not copied into memory and are instead read from calldata directly.
see reference: https://github.com/code-423n4/2021-06-gro-findings/issues/37

## Mitigation
Set function visibility to `external`

## Line of code
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398-L411
