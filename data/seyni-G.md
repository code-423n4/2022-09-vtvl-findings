# Gas Optimizations

## [G-01] Useless explicit initialization of variable to default value

Explicitly initializing a variable with its default value wastes gas.

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

### Findings :

```solidity
2022-09-vtvl/contracts/VTVLVesting.sol::27 	 uint112 public numTokensReservedForVesting = 0;
2022-09-vtvl/contracts/VTVLVesting.sol::148 	 uint112 vestAmt = 0;
2022-09-vtvl/contracts/VTVLVesting.sol::353 	 for (uint256 i = 0; i < length; i++) {
```

## [G-02] `i++` instead of `++i`

`i++` costs 5 more gas per loop than `++i`.

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

### Findings :

```solidity
2022-09-vtvl/contracts/VTVLVesting.sol::353 	 for (uint256 i = 0; i < length; i++) {
```

## [G-03] Inline `unchecked{++i;}` could be used

The increment of a loop can be inlined and unchecked since there is no risk of overflow/underflow, which costs less gas.

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

### Findings :

```solidity
2022-09-vtvl/contracts/VTVLVesting.sol::353 	 for (uint256 i = 0; i < length; i++) {
```

## [G-04] Boolean comparison

Using if(bool) or if(!bool) instead of if(bool == true) or if(bool == false) costs less gas.

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

### Findings :

```solidity
2022-09-vtvl/contracts/VTVLVesting.sol::111 	 require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

## [G-05] Use custom errors instead of revert string

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met).

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

### Findings :

```solidity
2022-09-vtvl/contracts/AccessProtected.sol::25 	 require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
2022-09-vtvl/contracts/AccessProtected.sol::40 	 require(admin != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/VTVLVesting.sol::82 	 require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/VTVLVesting.sol::107 	 require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
2022-09-vtvl/contracts/VTVLVesting.sol::111 	 require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
2022-09-vtvl/contracts/VTVLVesting.sol::129 	 require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
2022-09-vtvl/contracts/VTVLVesting.sol::255 	 require(_recipient != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/VTVLVesting.sol::256 	 require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
2022-09-vtvl/contracts/VTVLVesting.sol::257 	 require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
2022-09-vtvl/contracts/VTVLVesting.sol::262 	 require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
2022-09-vtvl/contracts/VTVLVesting.sol::263 	 require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
2022-09-vtvl/contracts/VTVLVesting.sol::264 	 require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
2022-09-vtvl/contracts/VTVLVesting.sol::270 	 require(
                                                    (
                                                        _cliffReleaseTimestamp > 0 &&
                                                        _cliffAmount > 0 &&
                                                        _cliffReleaseTimestamp <= _startTimestamp
                                                    ) || (
                                                        _cliffReleaseTimestamp == 0 &&
                                                        _cliffAmount == 0
                                                ), "INVALID_CLIFF");
2022-09-vtvl/contracts/VTVLVesting.sol::295 	 require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
2022-09-vtvl/contracts/VTVLVesting.sol::344 	 require(_startTimestamps.length == length &&
2022-09-vtvl/contracts/VTVLVesting.sol::374 	 require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
2022-09-vtvl/contracts/VTVLVesting.sol::402 	 require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
2022-09-vtvl/contracts/VTVLVesting.sol::426 	 require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
2022-09-vtvl/contracts/VTVLVesting.sol::447 	 require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
2022-09-vtvl/contracts/VTVLVesting.sol::449 	 require(bal > 0, "INSUFFICIENT_BALANCE");
2022-09-vtvl/contracts/token/FullPremintERC20Token.sol::11 	 require(supply_ > 0, "NO_ZERO_MINT");
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::27 	 require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::37 	 require(account != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::41 	 require(amount <= mintableSupply, "INVALID_AMOUNT");
```

## [G-06] Operations could be unchecked

The following operations could be unchecked to save gas, because `if(_referenceTs > _claim.startTimestamp)` (in \_baseVestedAmount) and `require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP")` (when creating a claim) checks are done.

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

### Findings :

```solidity
2022-09-vtvl/contracts/VTVLVesting.sol:167:      uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
2022-09-vtvl/contracts/VTVLVesting.sol:170:      uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval
```

## [G-07] `_baseVestedAmount(_claim, _claim.endTimestamp)` could be called instead of `finalVestedAmount(_recipient)`

Calling `_baseVestedAmount(_claim, _claim.endTimestamp)` instead of `finalVestedAmount(_recipient)` cost significantly less gas because an additional read from storage is done in `finalVestedAmount`.

I agree with the comment that says that the function `finalVestedAmount`should be removed.

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

### Findings :

```solidity
2022-09-vtvl/contracts/VTVLVesting.sol:422:     uint112 finalVestAmt = finalVestedAmount(_recipient);

2022-09-vtvl/contracts/VTVLVesting.sol:206:     function finalVestedAmount(address _recipient) public view returns (uint112) {
                                                    Claim storage _claim = claims[_recipient];
                                                    return _baseVestedAmount(_claim, _claim.endTimestamp);
                                                }
```