## Add unchecked

```solidity
    function withdraw() hasActiveClaim(_msgSender()) external {
        ...

        // Make sure we didn't already withdraw more that we're allowed.
        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

        unchecked {
        // Calculate how much can we withdraw (equivalent to the above inequality)
        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;

        // "Double-entry bookkeeping"
        // Carry out the withdrawal by noting the withdrawn amount, and by transferring the tokens.
        usrClaim.amountWithdrawn += amountRemaining;
        // Reduce the allocated amount since the following transaction pays out so the "debt" gets reduced
        numTokensReservedForVesting -= amountRemaining;
        }

        ...
    }
```

allowance > usrClaim.amountWithdrawn already check for over/underflow and amountRemaining should never overflow / underflow numTokensReservedForVesting or amountWithdrawn (allowance - usrClaim.amountWithdrawn + usrClaim.amountWithdrawn = allowance)

## Add unchecked
```
    function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
            ...

            // Calculate the linearly vested amount - this is relevant only if we're past the schedule start
            // at _referenceTs == _claim.startTimestamp, the period proportion will be 0 so we don't need to start the calc
            if(_referenceTs > _claim.startTimestamp) {
                uint40 currentVestingDurationSecs, truncatedCurrentVestingDurationSecs, finalVestingDurationSecs;

                unchecked {
                currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
                // Next, we need to calculated the duration truncated to nearest releaseIntervalSecs
                truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
                finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval
                }

                // Calculate the linear vested amount - fraction_of_interval_completed * linearVestedAmount
                // Since fraction_of_interval_completed is truncatedCurrentVestingDurationSecs / finalVestingDurationSecs, the formula becomes
                // truncatedCurrentVestingDurationSecs / finalVestingDurationSecs * linearVestAmount, so we can rewrite as below to avoid 
                // rounding errors
                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;

                // Having calculated the linearVestAmount, simply add it to the vested amount
                vestAmt += linearVestAmount;
            }
        }

       ...
    }
```

## Use custom errors instead of require

```
require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
```

Should be rewrite as

```
error NOTHING_TO_WITHDRAW();
...
if(allowance <= usrClaim.amountWithdrawn) revert NOTHING_TO_WITHDRAW();
```

## Unchecked { ++i }

```
        for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```

Should be rewrite as

```
        for (uint256 i = 0; i < length; ) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[I]);
            unchecked { ++i; }
        }
```