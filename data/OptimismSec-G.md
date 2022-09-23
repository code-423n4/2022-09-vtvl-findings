# Gas Report

## Use `memory` instead using of `storage` for storing the claims struct to some save gas

- Affected lines of code
  - [L106](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L106): 
  ```solidity
  Claim storage _claim = claims[_recipient];
  ```
  - [L124](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L124)
  ```solidity
  Claim storage _claim = claims[_recipient];
  ```
  - [L197](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L197)
  ```solidity
  Claim storage _claim = claims[_recipient];
  ```
  - [L207](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L207) 
  ```solidity
  Claim storage _claim = claims[_recipient];
  ```
  - [L216](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L216)
  ```solidity
  Claim storage _claim = claims[_recipient];
  ```
  - [L367](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L367) 
  ```solidity
  Claim storage usrClaim = claims[_msgSender()];
  ```
  - [L420](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L420)
  ```solidity
  Claim storage _claim = claims[_recipient];
  ```

## Use `unchecked` blocks and `++i` to make loops more efficient
  - [L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)
  - like so
 ```solidity
       │ File: contracts/VTVLVesting.sol
 353 + │         for (uint256 i = 0; i < length;) {
 354   │             _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffRelease
       │ Timestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
   +   |             unchecked {
   +   |                  ++i;
   +   |             }
   +   |
 355   │         }
 356   │ 
 ```
## The function `withdrawAdmin()` is only declared and not used anywhere else in the contract, make it external

See [L398](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398).

## Some calculations won't reasonably overflow
 
Use `unchecked` blocks for them to save gas.

The function is internal and the parameters are not controlled by the user, so it is safe to use `unchecked`.

[L166-L179](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L166-L179) can be changed as follows:
 
 ```solidity
──────┬────────────────────────────────────────────────────────────────────────────────────────────────────────
       │          File: contracts/VTVLVesting.sol#L166-L179

 166   │         if(_referenceTs > _claim.startTimestamp) {
     + |             unchecked {
 167   │                 uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
 168   │                 // Next, we need to calculated the duration truncated to nearest releaseIntervalSecs
 169   │                 uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
 170   │                 uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval
 171   │ 
 172   │                 // Calculate the linear vested amount - fraction_of_interval_completed * linearVestedAmount
 173   │                 // Since fraction_of_interval_completed is truncatedCurrentVestingDurationSecs / finalV estingDurationSecs, the formula becomes
 174   │                 // truncatedCurrentVestingDurationSecs / finalVestingDurationSecs * linearVestAmount, so we can rewrite as below to avoid 
 175   │                 // rounding errors
 176   │                 uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;
 177   │ 
 178   │                 // Having calculated the linearVestAmount, simply add it to the vested amount
 179   │                 vestAmt += linearVestAmount;
    +  |              }
             }
 ```