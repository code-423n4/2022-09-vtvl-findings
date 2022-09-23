* ```_cliffReleaseTimestamp > 0``` is not really needed here:
```solidity
  require( 
    _cliffReleaseTimestamp > 0 && 
    _cliffAmount > 0 && 
    _cliffReleaseTimestamp <= _startTimestamp
  )
```
because ```_startTimestamp``` was validated before:
```solidity
  require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
```

* No need to initialize to default values:
```solidity
  uint112 public numTokensReservedForVesting = 0;
```

* This is the first assignment to ```vestAmt```:
```solidity
  if(_referenceTs >= _claim.cliffReleaseTimestamp) {
      vestAmt += _claim.cliffAmount;
  }
```
can save a bit of gas by replacing ```+=``` with just ```=```.

* Can use ```unchecked``` maths here and everywhere else where overflow/underflow is not possible:
```solidity
  if(_referenceTs > _claim.startTimestamp) {
      uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp;
```

* The duration is re-calculated every time the ```_baseVestedAmount``` is queried:
```solidity
  uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp;
```
even though nor start, nor end timestamps do not change. Wouldn't it be better to store the duration in the ```Claim``` struct then?

* Repeated access of storage ```usrClaim.amountWithdrawn``` variable:
```solidity        
  require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
  ...
  uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
```

* Can just directly assign ```usrClaim.amountWithdrawn = allowance```:
```solidity 
  uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
  ...
  uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
  ...
  usrClaim.amountWithdrawn += amountRemaining;
```