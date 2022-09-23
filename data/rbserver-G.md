## [G-01] `usrClaim.amountWithdrawn` CAN BE CACHED IN MEMORY
`usrClaim.amountWithdrawn` in the following code can be cached in memory, and the cached value can then be accessed. This can save 2 `sload` operations by using 1 `sload`, 1 `mstore`, and 2 `mload` operations.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L373-L377
```solidity
        // Make sure we didn't already withdraw more that we're allowed.
        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

        // Calculate how much can we withdraw (equivalent to the above inequality)
        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
```

## [G-02] BOOLEAN VARIABLE DOES NOT NEED TO BE COMPARED TO BOOLEAN VALUE
Comparing a boolean variable to a boolean value costs more gas than directly checking the boolean variable value.

`require(_claim.isActive, "NO_ACTIVE_CLAIM")` can be implemented instead of the following code.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111
```solidity
        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

## [G-03] ARITHMETIC OPERATIONS THAT DO NOT UNDERFLOW CAN BE UNCHECKED
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

## [G-04] ARITHMETIC OPERATIONS THAT DO NOT OVERFLOW CAN BE UNCHECKED
Explicitly unchecking arithmetic operations that do not overflow by wrapping these in `unchecked {}` costs less gas than implicitly checking these.

For the following loop, `unchecked {++i}` at the end of the loop block can be used, where `i` is the counter variable.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353-L355
```solidity
        for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```

## [G-05] VARIABLE DOES NOT NEED TO BE INITIALIZED TO ITS DEFAULT VALUE
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

## [G-06] ++VARIABLE CAN BE USED INSTEAD OF VARIABLE++
++variable costs less gas than variable++. For example, `i++` can be changed to `++i` in the following code.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353-L355
```solidity
        for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```

## [G-07] X = X + Y CAN BE USED INSTEAD OF X += Y
x = x + y costs less gas than x += y. For example, `vestAmt += linearVestAmount` can be changed to `vestAmt = vestAmt + linearVestAmount` in the following code.

```solidity
contracts\VTVLVesting.sol
  161: vestAmt += _claim.cliffAmount;
  179: vestAmt += linearVestAmount;
  179: vestAmt += linearVestAmount;
  301: numTokensReservedForVesting += allocatedAmount; // track the allocated amount
  380: usrClaim.amountWithdrawn += amountRemaining;
```

## [G-08] X = X - Y CAN BE USED INSTEAD OF X -= Y
x = x - y costs less gas than x -= y. For example, `mintableSupply -= amount` can be changed to `mintableSupply = mintableSupply - amount` in the following code.
```solidity
contracts\VTVLVesting.sol
  382: numTokensReservedForVesting -= amountRemaining;
  382: numTokensReservedForVesting -= amountRemaining;
  432: numTokensReservedForVesting -= amountRemaining; // Reduces the allocation

contracts\token\VariableSupplyERC20Token.sol
  43: mintableSupply -= amount;
```

## [G-09] REVERT WITH CUSTOM ERROR CAN BE USED INSTEAD OF REQUIRE() WITH REASON STRING
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