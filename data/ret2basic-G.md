# VTVL Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (3 instances)
2. Use `unchecked{}` to suppress overflow/underflow check (1 instance)
3. Using `bool`s for storage incurs overhead (1 instance)
4. Use `!= 0` instead of `> 0` when comparing uint (11 instances)
5. Don't initialize variables with default value (2 instances)
6. Use `++i`/`--i` instead of `i++`/`i--` (1 instance)
7. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (1 instance)
8. Use custom errors instead of `revert()`/`require()` strings (22 instances)
9. Some functions can be declared as `external` to save gas (2 instances)

Total 44 instances of 9 issues.

## 1. Use `calldata` instead of `memory` (3 instances)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
contracts/VTVLVesting.sol::147 => function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {

contracts/token/FullPremintERC20Token.sol::10 => constructor(string memory name_, string memory symbol_, uint256 supply_) ERC20(name_, symbol_) {

contracts/token/VariableSupplyERC20Token.sol::21 => constructor(string memory name_, string memory symbol_, uint256 initialSupply_, uint256 maxSupply_) ERC20(name_, symbol_) {
```

## 2. Use `unchecked{}` to suppress overflow/underflow check (1 instance)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```

## 3. Using `bool`s for storage incurs overhead (1 instance)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
contracts/VTVLVesting.sol::45 => bool isActive; // whether this claim is active (or revoked)
```

## 4. Use `!= 0` instead of `> 0` when comparing uint (11 instances)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
contracts/VTVLVesting.sol::107 => require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

contracts/VTVLVesting.sol::256 => require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both

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

## 5. Don't initialize variables with default value (2 instances)

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```solidity
contracts/VTVLVesting.sol::148 => uint112 vestAmt = 0;

contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```

## 6. Use `++i`/`--i` instead of `i++`/`i--` (1 instance)

Using `++i`/`--i` saves 6 gas per loop.

```solidity
contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```

## 7. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (1 instance)

Instead of using operator && on single require check, using double `require()` checks can save more gas.

```solidity
contracts/VTVLVesting.sol::344-351

        require(_startTimestamps.length == length &&
                _endTimestamps.length == length &&
                _cliffReleaseTimestamps.length == length &&
                _releaseIntervalsSecs.length == length &&
                _linearVestAmounts.length == length &&
                _cliffAmounts.length == length, 
                "ARRAY_LENGTH_MISMATCH"
        );
```

## 8. Use custom errors instead of `revert()`/`require()` strings (22 instances)

Using `require()`/`revert()` strings is expensive. Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors.

Custom errors decrease both deploy and runtime gas costs. Note that runtime gas cost is only relevant when the revert condition is met.

```solidity
contracts/AccessProtected.sol::25 => require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");

contracts/AccessProtected.sol::40 => require(admin != address(0), "INVALID_ADDRESS");

contracts/VTVLVesting.sol::82 => require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");

contracts/VTVLVesting.sol::107 => require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

contracts/VTVLVesting.sol::111 => require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

contracts/VTVLVesting.sol::129 => require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");

contracts/VTVLVesting.sol::255 => require(_recipient != address(0), "INVALID_ADDRESS");

contracts/VTVLVesting.sol::256 => require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both

contracts/VTVLVesting.sol::257 => require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

contracts/VTVLVesting.sol::262 => require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp

contracts/VTVLVesting.sol::263 => require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

contracts/VTVLVesting.sol::264 => require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

contracts/VTVLVesting.sol::295 => require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

contracts/VTVLVesting.sol::374 => require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

contracts/VTVLVesting.sol::402 => require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");

contracts/VTVLVesting.sol::426 => require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

contracts/VTVLVesting.sol::447 => require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor

contracts/VTVLVesting.sol::449 => require(bal > 0, "INSUFFICIENT_BALANCE");

contracts/token/FullPremintERC20Token.sol::11 => require(supply_ > 0, "NO_ZERO_MINT");

contracts/token/VariableSupplyERC20Token.sol::27 => require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

contracts/token/VariableSupplyERC20Token.sol::37 => require(account != address(0), "INVALID_ADDRESS");

contracts/token/VariableSupplyERC20Token.sol::41 => require(amount <= mintableSupply, "INVALID_AMOUNT");
```

## 9. `setAdmin()` can be declared as `external` to save gas (1 instance)

`public` functions cost more gas than `external` functions since they handle both internal and external calls.

```solidity
contracts/AccessProtected.sol::39 => function setAdmin(address admin, bool isEnabled) public onlyAdmin {

contracts/token/VariableSupplyERC20Token.sol::36 => function mint(address account, uint256 amount) public onlyAdmin {
```
