### gas - no need to init to 0 and change to uint256 and declare functions external
   one of non exhaustive e.g. 
    ```
    uint112 public numTokensReservedForVesting = 0;
    ```

## gas - prefer prefix ++i instead of suffic i++ to save gas, no need to init i and better if used unchecked
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353-L355

```solidity
        for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```