## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-09-vtvl/contracts/VTVLVesting.sol::148 => uint112 vestAmt = 0;
2022-09-vtvl/contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```

## [G-02] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

```
2022-09-vtvl/contracts/VTVLVesting.sol::107 => require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
2022-09-vtvl/contracts/VTVLVesting.sol::114 => // require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
2022-09-vtvl/contracts/VTVLVesting.sol::115 => // require(_claim.endTimestamp > 0, "NO_END_TIMESTAMP");
2022-09-vtvl/contracts/VTVLVesting.sol::256 => require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
2022-09-vtvl/contracts/VTVLVesting.sol::257 => require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
2022-09-vtvl/contracts/VTVLVesting.sol::261 => // require(_endTimestamp > 0, "_endTimestamp must be valid"); // not necessary because of the next condition (transitively)
2022-09-vtvl/contracts/VTVLVesting.sol::263 => require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
2022-09-vtvl/contracts/VTVLVesting.sol::449 => require(bal > 0, "INSUFFICIENT_BALANCE");
2022-09-vtvl/contracts/token/FullPremintERC20Token.sol::11 => require(supply_ > 0, "NO_ZERO_MINT");
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::27 => require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```

## [G-03] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2022-09-vtvl/contracts/VTVLVesting.sol::147 => function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
```

## [G-04] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2022-09-vtvl/contracts/VTVLVesting.sol::17 => IERC20 public immutable tokenAddress;
```

## [G-05] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-09-vtvl/contracts/AccessProtected.sol::39 => function setAdmin(address admin, bool isEnabled) public onlyAdmin {
2022-09-vtvl/contracts/VTVLVesting.sol::398 => function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
2022-09-vtvl/contracts/VTVLVesting.sol::418 => function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {
2022-09-vtvl/contracts/VTVLVesting.sol::446 => function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::36 => function mint(address account, uint256 amount) public onlyAdmin {
```

## [G-06] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2022-09-vtvl/contracts/AccessProtected.sol::12 => mapping(address => bool) private _admins; // user address => admin? mapping
```

## [G-07] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-09-vtvl/contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```

## [G-08] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-09-vtvl/contracts/VTVLVesting.sol::161 => vestAmt += _claim.cliffAmount;
2022-09-vtvl/contracts/VTVLVesting.sol::179 => vestAmt += linearVestAmount;
2022-09-vtvl/contracts/VTVLVesting.sol::301 => numTokensReservedForVesting += allocatedAmount; // track the allocated amount
2022-09-vtvl/contracts/VTVLVesting.sol::381 => usrClaim.amountWithdrawn += amountRemaining;
2022-09-vtvl/contracts/VTVLVesting.sol::383 => numTokensReservedForVesting -= amountRemaining;
2022-09-vtvl/contracts/VTVLVesting.sol::433 => numTokensReservedForVesting -= amountRemaining; // Reduces the allocation
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::43 => mintableSupply -= amount;
```

## [G-09] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2022-09-vtvl/contracts/AccessProtected.sol::25 => require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
2022-09-vtvl/contracts/AccessProtected.sol::40 => require(admin != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/VTVLVesting.sol::82 => require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/VTVLVesting.sol::107 => require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
2022-09-vtvl/contracts/VTVLVesting.sol::111 => require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
2022-09-vtvl/contracts/VTVLVesting.sol::129 => require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
2022-09-vtvl/contracts/VTVLVesting.sol::255 => require(_recipient != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/VTVLVesting.sol::256 => require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
2022-09-vtvl/contracts/VTVLVesting.sol::257 => require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
2022-09-vtvl/contracts/VTVLVesting.sol::262 => require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
2022-09-vtvl/contracts/VTVLVesting.sol::263 => require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
2022-09-vtvl/contracts/VTVLVesting.sol::264 => require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
2022-09-vtvl/contracts/VTVLVesting.sol::295 => require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
2022-09-vtvl/contracts/VTVLVesting.sol::374 => require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
2022-09-vtvl/contracts/VTVLVesting.sol::402 => require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
2022-09-vtvl/contracts/VTVLVesting.sol::426 => require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
2022-09-vtvl/contracts/VTVLVesting.sol::447 => require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
2022-09-vtvl/contracts/VTVLVesting.sol::449 => require(bal > 0, "INSUFFICIENT_BALANCE");
2022-09-vtvl/contracts/token/FullPremintERC20Token.sol::11 => require(supply_ > 0, "NO_ZERO_MINT");
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::27 => require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::37 => require(account != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::41 => require(amount <= mintableSupply, "INVALID_AMOUNT");
```

## [G-10] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2022-09-vtvl/contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```

## [G-11] Don't compare boolean expressions to boolean literals

The extran comparison wastes gas
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

```
2022-09-vtvl/contracts/VTVLVesting.sol::111 => require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

## [G-12] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
2022-09-vtvl/contracts/VTVLVesting.sol::344 => require(_startTimestamps.length == length &&
```

## [G-13] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-09-vtvl/contracts/AccessProtected.sol::39 => function setAdmin(address admin, bool isEnabled) public onlyAdmin {
2022-09-vtvl/contracts/VTVLVesting.sol::398 => function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
```

## [G-14] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
2022-09-vtvl/contracts/AccessProtected.sol::40 => require(admin != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/VTVLVesting.sol::82 => require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/VTVLVesting.sol::255 => require(_recipient != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::37 => require(account != address(0), "INVALID_ADDRESS");
```

## [G-15] No need to check initialSupply_ > 0 twice

there is already an require statement above which checks for this condition

```
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L31
