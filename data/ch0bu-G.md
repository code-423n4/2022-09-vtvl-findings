## 1. `++i` costs less gas compared to `i++` or `i += 1`, same for `--i/i--`. Especially in for loops

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments i and returns the initial value of `i`.

```
uint i = 1;  
i++; // == 1 but i == 2
```
But ++i returns the actual incremented value:
```
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

I suggest using ++i instead of i++ to increment the value of an uint variable.

If done inside for loop, saves 6 gas per loop.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol
```
353	for (uint256 i = 0; i < length; i++) {
```

## 2. No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: `for (uint256 i = 0; i < length; ++i) {` should be replaced with for `(uint256 i; i < length; ++i) {`

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol
```
27	uint112 public numTokensReservedForVesting = 0;
148	uint112 vestAmt = 0;
353	for (uint256 i = 0; i < length; i++) {
```

## 3. Use custom errors instead of revert strings to save Gas


Custom errors from Solidity 0.8.4 are cheaper than revert string (cheaper deployment cost and runtime cost when the revert condition is met)

Source: https://blog.soliditylang.org/2021/04/21/custom-errors/:

> Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol
```
25	require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
40	require(admin != address(0), "INVALID_ADDRESS");
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol
```
82	require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
107	require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
111	require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
255	require(_recipient != address(0), "INVALID_ADDRESS");
256	require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
257	require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
262	require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
263	require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
264	require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
...
270	require( 
271 	(
272		_cliffReleaseTimestamp > 0 && 
273         _cliffAmount > 0 && 
274         _cliffReleaseTimestamp <= _startTimestamp
275         ) || (
276             _cliffReleaseTimestamp == 0 && 
277             _cliffAmount == 0
278     ), "INVALID_CLIFF");
...
295	require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
...
344	require(_startTimestamps.length == length &&
345 	_endTimestamps.length == length &&
346     _cliffReleaseTimestamps.length == length &&
347     _releaseIntervalsSecs.length == length &&
348     _linearVestAmounts.length == length &&
349     _cliffAmounts.length == length, 
350     "ARRAY_LENGTH_MISMATCH"
351     );
...
374	require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
402	require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
426	require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
447	require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
449	require(bal > 0, "INSUFFICIENT_BALANCE");
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol
```
11	require(supply_ > 0, "NO_ZERO_MINT");
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol
```
27	require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
37	require(account != address(0), "INVALID_ADDRESS");
41	require(amount <= mintableSupply, "INVALID_AMOUNT");
```

## 4. Splitting require() statements that use && saves gas


Instead of using the && operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement

Link to the issue: https://github.com/code-423n4/2022-01-xdefi-findings/issues/128

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol
```
270	require( 
271         (
272             _cliffReleaseTimestamp > 0 && 
273             _cliffAmount > 0 && 
274             _cliffReleaseTimestamp <= _startTimestamp
275         ) || (
276             _cliffReleaseTimestamp == 0 && 
277             _cliffAmount == 0
278     ), "INVALID_CLIFF");
...
344	require(_startTimestamps.length == length &&
345             _endTimestamps.length == length &&
346             _cliffReleaseTimestamps.length == length &&
347             _releaseIntervalsSecs.length == length &&
348             _linearVestAmounts.length == length &&
349             _cliffAmounts.length == length, 
350             "ARRAY_LENGTH_MISMATCH"
351     );
```

## 5. Using > 0 costs more gas than != 0 for Unsigned Integers

This change saves 6 gas per instance

Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

I suggest changing > 0 with != 0 here

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol
```
27	require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol
```
11	require(supply_ > 0, "NO_ZERO_MINT");
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol
```
256	require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
257	require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
263	require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
...
270     require( 
271         (
272             _cliffReleaseTimestamp > 0 && 
273             _cliffAmount > 0 && 
...
449	require(bal > 0, "INSUFFICIENT_BALANCE");
```

## 6. Increments can be unchecked


In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline. This saves 30-40 gas PER LOOP.


https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol
```
353	for (uint256 i = 0; i < length; i++) {
```

## 7. Boolean comparisons


Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(directValue) instead of if(directValue == true)

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol
```
111	require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

## 8. 13. Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

## 9. Using bools for storage incurs overhead


// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and 
// pointer aliasing, and it cannot be disabled.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

Use uint256(1) and uint256(2) for true/false

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol
```
12	mapping(address => bool) private _admins; // user address => admin? mapping
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol
```
45	bool isActive; // whether this claim is active (or revoked)
```

## 10. Not using the named return variables when a function returns, wastes deployment gas


https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol
```
45	return _admins[_addressToCheck];
```
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol
```
92	return claims[_recipient];
198	return _baseVestedAmount(_claim, _referenceTs);
208	return _baseVestedAmount(_claim, _claim.endTimestamp);
217	return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
```



