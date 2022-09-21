### [GO01] Don't Initialize Variables with Default Value

#### Impact

Uninitialized variables are assigned with the types default value.
Explicitly initializing a variable with it's default value costs unnecessary gas.

#### Findings:
```solidity
contracts/VTVLVesting.sol::148 => uint112 vestAmt = 0;
contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```

### [GO02] Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
// from
 (amount > 0);

// to
(amount != 0);
```

#### Findings:
```solidity
contracts/VTVLVesting.sol::107 => require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
contracts/VTVLVesting.sol::256 => require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
contracts/VTVLVesting.sol::257 => require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
contracts/VTVLVesting.sol::263 => require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
contracts/VTVLVesting.sol::272 => _cliffReleaseTimestamp > 0 &&
contracts/VTVLVesting.sol::273 => _cliffAmount > 0 &&
contracts/VTVLVesting.sol::449 => require(bal > 0, "INSUFFICIENT_BALANCE");
contracts/token/FullPremintERC20Token.sol::11 => require(supply_ > 0, "NO_ZERO_MINT");
contracts/token/VariableSupplyERC20Token.sol::27 => require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
contracts/token/VariableSupplyERC20Token.sol::31 => if(initialSupply_ > 0) {
contracts/token/VariableSupplyERC20Token.sol::40 => if(mintableSupply > 0) {
```

### [GO03] Use Custom Errors instead of Revert Strings to save Gas

#### Impact

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met). I suggest replacing all revert strings with custom errors.
