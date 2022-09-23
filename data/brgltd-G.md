# [G-01] Prefix increment costs less gas than postfix increment

There's 1 instance of this issue.

```solidity
File: contracts/VTVLVesting.sol
353: for (uint256 i = 0; i < length; i++) {
```

# [G-02] The increment in for loop post condition can be made unchecked to save gas

There's 1 instance of this issue.

```solidity
File: contracts/VTVLVesting.sol
353: for (uint256 i = 0; i < length; i++) {
```

# [G-03] Initializing a variable with the default value wastes gas

There are 2 instances of this issue.

```solidity
File: contracts/VTVLVesting.sol
148: uint112 vestAmt = 0;
353: for (uint256 i = 0; i < length; i++) {
```

# [G-04] Use != 0 instead of > 0 to save gas

Replace `> 0` with `!= 0` for unsigned integers.

There are 11 instances of this issue.

```solidity
File: contracts/VTVLVesting.sol
107: require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
256: require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
257: require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
263: require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
272: _cliffReleaseTimestamp > 0 &&
273: _cliffAmount > 0 &&
449: require(bal > 0, "INSUFFICIENT_BALANCE");
```

```solidity
File: contracts/token/FullPremintERC20Token.sol
11: require(supply_ > 0, "NO_ZERO_MINT");
```

```solidity
File: contracts/token/VariableSupplyERC20Token.sol
27: require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
31: if(initialSupply_ > 0) {
40: if(mintableSupply > 0) {
```

# [G-05] Use custom errors rather than require/revert strings to save gas

There are 21 instances of this issue.

```solidity
File: contracts/AccessProtected.sol
25: require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
40: require(admin != address(0), "INVALID_ADDRESS");
```

```solidity
File: contracts/VTVLVesting.sol
82: require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
107: require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
111: require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
255: require(_recipient != address(0), "INVALID_ADDRESS");
256: require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
257: require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
262: require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
263: require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
264: require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
295: require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
374: require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
402: require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
426: require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
447: require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
449: require(bal > 0, "INSUFFICIENT_BALANCE");
```

```solidity
File: contracts/token/FullPremintERC20Token.sol
11: require(supply_ > 0, "NO_ZERO_MINT");
```

```solidity
File: contracts/token/VariableSupplyERC20Token.sol
27: require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
37: require(account != address(0), "INVALID_ADDRESS");
41: require(amount <= mintableSupply, "INVALID_AMOUNT");
```

# [G-06] x += y costs more gas than x = x + y for state variables

There are 5 instances of this issue.

```solidity
File: contracts/VTVLVesting.sol
301: numTokensReservedForVesting += allocatedAmount; // track the allocated amount
381: usrClaim.amountWithdrawn += amountRemaining;
383: numTokensReservedForVesting -= amountRemaining;
433: numTokensReservedForVesting -= amountRemaining; // Reduces the allocation
```

```solidity
File: contracts/token/VariableSupplyERC20Token.sol
43: mintableSupply -= amount;
```

# [G-07] Don’t compare boolean expressions to boolean literals

There's 1 instance of this issue.

```solidity
File: contracts/VTVLVesting.sol
111: require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```
