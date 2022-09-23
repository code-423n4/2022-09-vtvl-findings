## G-01 Don't compare boolean expressions to boolean literals

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value. I suggest using `if(!directValue)` instead of `if(directValue == false)`

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111

```
File: contracts/VTVLVesting.sol    Line: 111

require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

-------------

## G-02 Using greater than 0 costs more gas than != 0 when used on a uint in a require() statement

!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas). This change saves 6 gas per instance

There are 8 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol

107:     require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
256:     require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
257:     require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
263:     require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
270-273:    require( 
            (
                _cliffReleaseTimestamp > 0 && 
                _cliffAmount > 0 && 
449:     require(bal > 0, "INSUFFICIENT_BALANCE");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11

```
File: contracts/token/FullPremintERC20Token.sol    Line: 11

require(supply_ > 0, "NO_ZERO_MINT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

```
File: contracts/token/VariableSupplyERC20Token.sol

27: require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```

--------------

## G-03 Use an unchecked block for calculations that cannot overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block:
https://docs.soliditylang.org/en/v0.8.7/control-structures.html#checked-or-unchecked-arithmetic

There are 18 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol

161: vestAmt += _claim.cliffAmount;
169: uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
170: uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; 
176: uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;
179: vestAmt += linearVestAmount;
217: return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
256: require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
264: require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
292: uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
295: require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
301: numTokensReservedForVesting += allocatedAmount;
377: uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
381: usrClaim.amountWithdrawn += amountRemaining;
383: numTokensReservedForVesting -= amountRemaining;
400: uint256 amountRemaining = tokenAddress.balanceOf(address(this)) - numTokensReservedForVesting;
429: uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
433: numTokensReservedForVesting -= amountRemaining;
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43

```
File: contracts/token/VariableSupplyERC20Token.sol    Line: 43

mintableSupply -= amount;
```

------------

## G-04 Increments can be unchecked

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

```
File: contracts/VTVLVesting.sol Line: 353

353: for (uint256 i = 0; i < length; i++){}
```

-----------

## G-05 It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 3 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol 

27: uint112 public numTokensReservedForVesting = 0;
148: uint112 vestAmt = 0;
353: for (uint256 i = 0; i < length; i++) {
```

--------------------

## G-06 x += y costs more gas than x = x+y for state variables

There are 7 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol 

161: vestAmt += _claim.cliffAmount;
179: vestAmt += linearVestAmount;
301: numTokensReservedForVesting += allocatedAmount;
381: usrClaim.amountWithdrawn += amountRemaining;
383: numTokensReservedForVesting -= amountRemaining;
433: numTokensReservedForVesting -= amountRemaining;
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43

```
File: contracts/token/VariableSupplyERC20Token.sol Line: 43

mintableSupply -= amount;
```

-----------

## G-07 Use custom errors rather than revert() or require() strings to save deployment gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

Source: [https://blog.soliditylang.org/2021/04/21/custom-errors/](https://blog.soliditylang.org/2021/04/21/custom-errors/):

> Starting from [Solidity v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4), there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., `revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the `error` statement, which can be used inside and outside of contracts (including interfaces and libraries).

There are 24 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol

```
File: contracts/AccessProtected.sol

25: require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
40: require(admin != address(0), "INVALID_ADDRESS");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol

82: require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
107: require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
111: require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
129: require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
255: require(_recipient != address(0), "INVALID_ADDRESS");
256: require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
257: require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
262: require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP");
263: require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
264: require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
270-278: require(
(
_cliffReleaseTimestamp > 0 &&
_cliffAmount > 0 &&
_cliffReleaseTimestamp <= _startTimestamp
) || (
_cliffReleaseTimestamp == 0 &&
_cliffAmount == 0
), "INVALID_CLIFF");
295: require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
344-350: require(_startTimestamps.length == length &&
_endTimestamps.length == length &&
_cliffReleaseTimestamps.length == length &&
_releaseIntervalsSecs.length == length &&
_linearVestAmounts.length == length &&
_cliffAmounts.length == length,
"ARRAY_LENGTH_MISMATCH"
374: require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
402: require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
426: require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
447: require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN");
449: require(bal > 0, "INSUFFICIENT_BALANCE");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11

```
File: contracts/token/FullPremintERC20Token.sol    Line: 11

require(supply_ > 0, "NO_ZERO_MINT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

```
File: contracts/token/VariableSupplyERC20Token.sol

27: require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
37: require(account != address(0), "INVALID_ADDRESS");
41: require(amount <= mintableSupply, "INVALID_AMOUNT");
```

-------------------------

## G-08 Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

There are 7 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39

```
File: contracts/AccessProtected.sol    Line: 39

function setAdmin(address admin, bool isEnabled) public onlyAdmin {
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol

317-325: function createClaim(
address _recipient,
uint40 _startTimestamp,
uint40 _endTimestamp,
uint40 _cliffReleaseTimestamp,
uint40 _releaseIntervalSecs,
uint112 _linearVestAmount,
uint112 _cliffAmount
) external onlyAdmin {
333-341: function createClaimsBatch(
address[] memory _recipients,
uint40[] memory _startTimestamps,
uint40[] memory _endTimestamps,
uint40[] memory _cliffReleaseTimestamps,
uint40[] memory _releaseIntervalsSecs,
uint112[] memory _linearVestAmounts,
uint112[] memory _cliffAmounts)
external onlyAdmin {
398: function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
418: function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {
446: function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36

```
File: contracts/token/VariableSupplyERC20Token.sol    Line: 36

function mint(address account, uint256 amount) public onlyAdmin {
```

--------------

## G-09 Using greater than 0 costs more gas than != 0 when used on a uint in a require() statement

`msg.sender` costs 2 gas (CALLER opcode). `_msgSender()` represents the following:

```
function _msgSender() internal view virtual returns (address payable) {  return msg.sender;}
```

When no meta-transactions capabilities are used: `msg.sender` is enough.

Consider replacing `_msgSender()` with `msg.sender` here

There are 13 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol

```
File: contracts/AccessProtected.sol

17: _admins[_msgSender()] = true;
18: emit AdminAccessSet(_msgSender(), true);
25: require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol

364: function withdraw() hasActiveClaim(_msgSender()) external {
367: Claim storage usrClaim = claims[_msgSender()];
371: uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
388: tokenAddress.safeTransfer(_msgSender(), amountRemaining);
391: emit Claimed(_msgSender(), amountRemaining);
407: tokenAddress.safeTransfer(_msgSender(), _amountRequested);
410: emit AdminWithdrawn(_msgSender(), _amountRequested);
450: _otherTokenAddress.safeTransfer(_msgSender(), bal);
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L12

```
File: contracts/token/FullPremintERC20Token.sol    Line: 12

_mint(_msgSender(), supply_);
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L32

```
File: contracts/token/VariableSupplyERC20Token.sol    Line: 32

mint(_msgSender(), initialSupply_);
```

----------------

## G-10 Usage of uints or ints smaller than 32 bytes (26 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

EVM (Ethereum Virtual Machine) is driven by 256 bit (32 bytes) variables. This means that in our loop above, the `uint16` variable is converted to a `uint256` before it is used, this conversion costs gas.

There are 46 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol

27: uint112 public numTokensReservedForVesting = 0;
35: uint40 startTimestamp;
36: uint40 endTimestamp;
37: uint40 cliffReleaseTimestamp;
38: uint40 releaseIntervalSecs;
42: uint112 linearVestAmount;
43: uint112 cliffAmount;
44: uint112 amountWithdrawn;
64: event Claimed(address indexed _recipient, uint112 _withdrawalAmount);
69: event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
74: event AdminWithdrawn(address indexed _recipient, uint112 _amountRequested);
147: function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
148: uint112 vestAmt = 0;
167: uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp;
169: uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
170: uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp;
176: uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;
196: function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
206: function finalVestedAmount(address _recipient) public view returns (uint112) {
215: function claimableAmount(address _recipient) external view returns (uint112) {
217: return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
247: uint40 _startTimestamp,
248: uint40 _endTimestamp,
249: uint40 _cliffReleaseTimestamp,
250: uint40 _releaseIntervalSecs,
251: uint112 _linearVestAmount,
252: uint112 _cliffAmount
292: uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
319: uint40 _startTimestamp,
320: uint40 _endTimestamp,
321: uint40 _cliffReleaseTimestamp,
322: uint40 _releaseIntervalSecs,
323: uint112 _linearVestAmount,
324: uint112 _cliffAmount
335: uint40[] memory _startTimestamps,
336: uint40[] memory _endTimestamps,
337: uint40[] memory _cliffReleaseTimestamps,
338: uint40[] memory _releaseIntervalsSecs,
339: uint112[] memory _linearVestAmounts,
340: uint112[] memory _cliffAmounts)
371: uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
377: uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
398: function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
422: uint112 finalVestAmt = finalVestedAmount(_recipient);
429: uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
436: emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);
```

-------------

## G-11 Non-strict inequalities are cheaper than strict ones

There are 2 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol

154: if(_referenceTs > _claim.endTimestamp) {
166: if(_referenceTs > _claim.startTimestamp) {
```
--------

## G-12 Splitting require() statements that use && saves gas

There are 2 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol

270-277: require( 
            (
                _cliffReleaseTimestamp > 0 && 
                _cliffAmount > 0 && 
                _cliffReleaseTimestamp <= _startTimestamp
            ) || (
                _cliffReleaseTimestamp == 0 && 
                _cliffAmount == 0
344-349: require(_startTimestamps.length == length &&
                _endTimestamps.length == length &&
                _cliffReleaseTimestamps.length == length &&
                _releaseIntervalsSecs.length == length &&
                _linearVestAmounts.length == length &&
                _cliffAmounts.length == length, 
```

----------

## G-13 ++i costs less gas than i++ especially when it's used in for loops (Same applies for --i , i-- too)

Prefix increments are cheaper than postfix increments.  

Further more, using unchecked {++i} is even more gas efficient, and the gas saving accumulates every iteration and can make a real change  
There is no risk of overflow caused by increamenting the iteration index in for loops (the `++i` in `for (uint256 i = 0; i < length; ++i) {`).  
But increments perform overflow checks that are not necessary in this case.  
These functions use not using prefix increments (`++i`) or not using the unchecked keyword

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

```
File: contracts/VTVLVesting.sol Line:353

for (uint256 i = 0; i < length; i++) {
```

-----------------