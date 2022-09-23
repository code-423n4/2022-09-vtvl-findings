## [G-01] SOME OPERATIONS CAN BE SKIPPED WHEN CLAIM'S `linearVestAmount` IS 0
It is possible to create a claim with 0 `linearVestAmount`; this is confirmed by the [documentation](https://github.com/code-423n4/2022-09-vtvl#claim) as well, which states: "Each of the parts (cliff and linear) have amounts that can be allocated to each. The founders can opt to use either or both options for each of the claims." When `linearVestAmount` is 0, `vestAmt` would be increased by 0 after executing the operations within the `_referenceTs > _claim.startTimestamp` `if` block in the following `_baseVestedAmount` function. Hence, these operations can be conditionally skipped if `linearVestAmount` is 0 to save gas.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L147-L188
```solidity
    function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
        uint112 vestAmt = 0;
        
        // the condition to have anything vested is to be active
        if(_claim.isActive) {
            // no point of looking past the endTimestamp as nothing should vest afterwards
            // So if we're past the end, just get the ref frame back to the end
            if(_referenceTs > _claim.endTimestamp) {
                _referenceTs = _claim.endTimestamp;
            }

            // If we're past the cliffReleaseTimestamp, we release the cliffAmount
            // We don't check here that cliffReleaseTimestamp is after the startTimestamp 
            if(_referenceTs >= _claim.cliffReleaseTimestamp) {
                vestAmt += _claim.cliffAmount;
            }

            // Calculate the linearly vested amount - this is relevant only if we're past the schedule start
            // at _referenceTs == _claim.startTimestamp, the period proportion will be 0 so we don't need to start the calc
            if(_referenceTs > _claim.startTimestamp) {
                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
                // Next, we need to calculated the duration truncated to nearest releaseIntervalSecs
                uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
                uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval

                // Calculate the linear vested amount - fraction_of_interval_completed * linearVestedAmount
                // Since fraction_of_interval_completed is truncatedCurrentVestingDurationSecs / finalVestingDurationSecs, the formula becomes
                // truncatedCurrentVestingDurationSecs / finalVestingDurationSecs * linearVestAmount, so we can rewrite as below to avoid 
                // rounding errors
                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;

                // Having calculated the linearVestAmount, simply add it to the vested amount
                vestAmt += linearVestAmount;
            }
        }
        
        // Return the bigger of (vestAmt, _claim.amountWithdrawn)
        // Rationale: no matter how we calculate the vestAmt, we can never return that less was vested than was already withdrawn.
        // Case where this could be relevant - If the claim is inactive, vestAmt would be 0, yet if something was already withdrawn 
        // on that claim, we want to return that as vested thus far - as we want the function to be monotonic.
        return (vestAmt > _claim.amountWithdrawn) ? vestAmt : _claim.amountWithdrawn;
    }
```

## [G-02] `usrClaim.amountWithdrawn` CAN BE CACHED IN MEMORY
`usrClaim.amountWithdrawn` in the following code can be cached in memory, and the cached value can then be accessed. This can save 2 `sload` operations by using 1 `sload`, 1 `mstore`, and 2 `mload` operations.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L373-L377
```solidity
        // Make sure we didn't already withdraw more that we're allowed.
        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

        // Calculate how much can we withdraw (equivalent to the above inequality)
        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
```

## [G-03] BOOLEAN VARIABLE DOES NOT NEED TO BE COMPARED TO BOOLEAN VALUE
Comparing a boolean variable to a boolean value costs more gas than directly checking the boolean variable value.

`require(_claim.isActive, "NO_ACTIVE_CLAIM")` can be implemented instead of the following code.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111
```solidity
        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

## [G-04] ARITHMETIC OPERATIONS THAT DO NOT UNDERFLOW CAN BE UNCHECKED
Explicitly unchecking arithmetic operations that do not underflow by wrapping these in `unchecked {}` costs less gas than implicitly checking these.

`_referenceTs - _claim.startTimestamp` can be unchecked in the following code because `_referenceTs > _claim.startTimestamp` must be true beforehand.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L166-L167
```solidity
            if(_referenceTs > _claim.startTimestamp) {
                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
```

`allowance - usrClaim.amountWithdrawn` can be unchecked in the following code because `allowance > usrClaim.amountWithdrawn` must be true beforehand.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L374-L377
```solidity
        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

        // Calculate how much can we withdraw (equivalent to the above inequality)
        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
```

`finalVestAmt - _claim.amountWithdrawn` can be unchecked in the following code because `_claim.amountWithdrawn < finalVestAmt` must be true beforehand.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426-L429
```solidity
        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

        // The amount that is "reclaimed" is equal to the total allocation less what was already withdrawn
        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
```

## [G-05] ARITHMETIC OPERATIONS THAT DO NOT OVERFLOW CAN BE UNCHECKED
Explicitly unchecking arithmetic operations that do not overflow by wrapping these in `unchecked {}` costs less gas than implicitly checking these.

For the following loop, `unchecked {++i}` at the end of the loop block can be used, where `i` is the counter variable.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353-L355
```solidity
        for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```

## [G-06] VARIABLE DOES NOT NEED TO BE INITIALIZED TO ITS DEFAULT VALUE
Explicitly initializing a variable with its default value costs more gas than uninitializing it. 

`numTokensReservedForVesting` does not need to be initialized to its default value in the following code.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
```solidity
    uint112 public numTokensReservedForVesting = 0;
```

`uint256 i` can be used instead of `uint256 i = 0` in the following code.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353-L355
```solidity
        for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```

## [G-07] ++VARIABLE CAN BE USED INSTEAD OF VARIABLE++
++variable costs less gas than variable++. For example, `i++` can be changed to `++i` in the following code.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353-L355
```solidity
        for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```

## [G-08] X = X + Y CAN BE USED INSTEAD OF X += Y
x = x + y costs less gas than x += y. For example, `vestAmt += linearVestAmount` can be changed to `vestAmt = vestAmt + linearVestAmount` in the following code.

```solidity
contracts\VTVLVesting.sol
  161: vestAmt += _claim.cliffAmount;
  179: vestAmt += linearVestAmount;
  179: vestAmt += linearVestAmount;
  301: numTokensReservedForVesting += allocatedAmount; // track the allocated amount
  380: usrClaim.amountWithdrawn += amountRemaining;
```

## [G-09] X = X - Y CAN BE USED INSTEAD OF X -= Y
x = x - y costs less gas than x -= y. For example, `mintableSupply -= amount` can be changed to `mintableSupply = mintableSupply - amount` in the following code.
```solidity
contracts\VTVLVesting.sol
  382: numTokensReservedForVesting -= amountRemaining;
  382: numTokensReservedForVesting -= amountRemaining;
  432: numTokensReservedForVesting -= amountRemaining; // Reduces the allocation

contracts\token\VariableSupplyERC20Token.sol
  43: mintableSupply -= amount;
```

## [G-10] REVERT WITH CUSTOM ERROR CAN BE USED INSTEAD OF REQUIRE() WITH REASON STRING
`revert` with custom error can cost less gas than `require()` with reason string. Please consider using `revert` with custom error to replace the following `require()`.
```solidity
contracts\AccessProtected.sol
  25: require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
  40: require(admin != address(0), "INVALID_ADDRESS");

contracts\VTVLVesting.sol
  82: require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
  107: require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
  111: require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
  129: require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
  255: require(_recipient != address(0), "INVALID_ADDRESS");
  256: require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
  257: require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
  262: require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
  263: require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
  264: require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
  270: require( 
  295: require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
  344: require(_startTimestamps.length == length &&
  373: require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
  401: require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
  425: require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
  446: require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
  448: require(bal > 0, "INSUFFICIENT_BALANCE");

contracts\token\FullPremintERC20Token.sol
  11: require(supply_ > 0, "NO_ZERO_MINT");

contracts\token\VariableSupplyERC20Token.sol
  27: require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
  37: require(account != address(0), "INVALID_ADDRESS");
  41: require(amount <= mintableSupply, "INVALID_AMOUNT");
```