| |Issue|Instances|
|-|:-|:-:|
| 1 | Caching storage variables in memory to save gas | 4 |
| 2 | Don't compare boolean expressions to boolean literals | 1 |
| 3 | `x += y` costs more gas than `x = x + y` for state variables | 5 |
| 4 | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 7 |
| 5 | Functions guaranteed to revert when called by normal users can be marked `payable` | 5 |
| 6 | Splitting `require()` statements that use `&&` saves gas | 1 |
| 7 | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 1 |
| 8 | Use custom errors rather than `revert()`/`require()` strings to save gas | 24 |
| 9 | `++variable` costs less gas than `variable++`, especially when it’s used in for-loops | 1 |
| 10 | Unchecked arithmetic | 13 |
| 11 | Default value initialization | 1 |
| 12 | Cache calculations | 1 |

## [G-01] Caching storage variables in memory to save gas
 Caching will replace each Gwarmaccess (100 gas) with a much cheaper stack read.
### Findings
```solidity
contracts/token/VariableSupplyERC20Token.sol
40:         if(mintableSupply > 0) {
41:             require(amount <= mintableSupply, "INVALID_AMOUNT");

43:             mintableSupply -= amount;
```
`mintableSupply` accessed three times

```solidity
contracts/VTVLVesting.sol
295:         require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

301:         numTokensReservedForVesting += allocatedAmount; // track the allocated amount
```
`numTokensReservedForVesting` accessed two times

```solidity
contracts/VTVLVesting.sol
374:         require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

377:         uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;

381:         usrClaim.amountWithdrawn += amountRemaining;
```
`usrClaim.amountWithdrawn` accessed three times

```solidity
contracts/VTVLVesting.sol
426:         require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

429:         uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
```
`_claim.amountWithdrawn` accessed two times

### Mitigation
Assign storage value to memory variable and use this variable instead of loading from storage multipple times.


## [G-02] Don't compare boolean expressions to boolean literals
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

### Findings
```solidity
contracts/VTVLVesting.sol
111:         require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

### Mitigation
```diff
contracts/VTVLVesting.sol
- 111:         require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
+ 111:         require(_claim.isActive, "NO_ACTIVE_CLAIM");
```

## [G-03] `x += y` costs more gas than `x = x + y` for state variables
`x += y` costs more than `x = x + y`
same as `x -= y`

### Findings
```solidity
contracts/VTVLVesting.sol
301:         numTokensReservedForVesting += allocatedAmount;

381:         usrClaim.amountWithdrawn += amountRemaining;

383:         numTokensReservedForVesting -= amountRemaining;

433:         numTokensReservedForVesting -= amountRemaining; // Reduces the allocation

contracts/token/VariableSupplyERC20Token.sol
43:             mintableSupply -= amount;

```
### Mitigation
Replace `x += y` and `x -= y` with `x = x + y` and `x = x - y`.

## [G-04] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
See the warning at this link: <https://docs.soliditylang.org/en/v0.8.0/internals/layout_in_storage.html#layout-of-state-variables-in-storage> :

> When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
> It is only beneficial to use reduced-size arguments if you are dealing with storage values because the compiler will pack multiple elements into one storage slot, and thus, combine multiple reads or writes into a single operation. When dealing with function arguments or memory values, there is no inherent benefit because the compiler does not pack these values.

### Findings

```solidity
contracts/
VTVLVesting.sol
148:         uint112 vestAmt = 0;

167:                 uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start

169:                 uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
170:                 uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval

176:                 uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;

196:     function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {

217:         return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;//@audit unchecked{}

```

### Mitigation
Generally use the `uint256` type and convert it to smaller type when you want to store a value.

## [G-05] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
### Findings
```solidity
contracts/VTVLVesting.sol
317:     function createClaim(
318:             address _recipient, 
319:             uint40 _startTimestamp, 
320:             uint40 _endTimestamp, 
321:             uint40 _cliffReleaseTimestamp, 
322:             uint40 _releaseIntervalSecs, 
323:             uint112 _linearVestAmount, 
324:             uint112 _cliffAmount
325:                 ) external onlyAdmin {

333:     function createClaimsBatch(
334:         address[] memory _recipients, 
335:         uint40[] memory _startTimestamps, 
336:         uint40[] memory _endTimestamps, 
337:         uint40[] memory _cliffReleaseTimestamps, 
338:         uint40[] memory _releaseIntervalsSecs, 
339:         uint112[] memory _linearVestAmounts, 
340:         uint112[] memory _cliffAmounts) 
341:         external onlyAdmin {

398:     function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    

418:     function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {

446:     function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
```

### Mitigation 
Add `payable` keyword to a function declaration

## [G-06] Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper
### Findings
```solidity
contracts/VTVLVesting.sol
344:         require(_startTimestamps.length == length &&
345:                 _endTimestamps.length == length &&
346:                 _cliffReleaseTimestamps.length == length &&
347:                 _releaseIntervalsSecs.length == length &&
348:                 _linearVestAmounts.length == length &&
349:                 _cliffAmounts.length == length, 
350:                 "ARRAY_LENGTH_MISMATCH"
351:         );
```

<https://github.com/code-423n4/2022-06-notional-coop/blob/6f8c325f604e2576e2fe257b6b57892ca181509a/notional-wrapped-fcash/contracts/wfCashLogic.sol#L116-L123>

### Mitigation
Instead of using the `&&` operator in a single require statement to check multiple conditions, we can use multiple require statements with 1 condition per require statement (saving 3 gas per `&`)

## [G-07] Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. 
If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it’s still more gass-efficient to use calldata when the external function does checks, since the checks may prevent the internal functions from being called. 
### Findings
```solidity
contracts/VTVLVesting.sol
333:     function createClaimsBatch(
334:         address[] memory _recipients, 
335:         uint40[] memory _startTimestamps, 
336:         uint40[] memory _endTimestamps, 
337:         uint40[] memory _cliffReleaseTimestamps, 
338:         uint40[] memory _releaseIntervalsSecs, 
339:         uint112[] memory _linearVestAmounts, 
340:         uint112[] memory _cliffAmounts) 
341:         external onlyAdmin {
```
### Mitigation
Replace `memory` with `calldata`

##  [G-08] Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors from Solidity 0.8.4 are cheaper than revert strings 
(cheaper deployment cost and runtime cost when the revert condition is met) 
while providing the same amount of information, as explained here
https://blog.soliditylang.org/2021/04/21/custom-errors/

### Findings
```solidity
contracts/VTVLVesting.sol
82:         require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");

107:         require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

111:         require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

129:         require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");

255:         require(_recipient != address(0), "INVALID_ADDRESS");
256:         require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
257:         require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

262:         require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
263:         require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
264:         require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

270:         require( 
271:             (
272:                 _cliffReleaseTimestamp > 0 && 
273:                 _cliffAmount > 0 && 
274:                 _cliffReleaseTimestamp <= _startTimestamp
275:             ) || (
276:                 _cliffReleaseTimestamp == 0 && 
277:                 _cliffAmount == 0
278:         ), "INVALID_CLIFF");

295:         require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

344:         require(_startTimestamps.length == length &&
345:                 _endTimestamps.length == length &&
346:                 _cliffReleaseTimestamps.length == length &&
347:                 _releaseIntervalsSecs.length == length &&
348:                 _linearVestAmounts.length == length &&
349:                 _cliffAmounts.length == length, 
350:                 "ARRAY_LENGTH_MISMATCH"
351:         );

374:         require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

402:         require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");

426:         require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

447:         require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor

449:         require(bal > 0, "INSUFFICIENT_BALANCE");
```

```solidity
contracts/AccessProtected.sol
25:         require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");

40:         require(admin != address(0), "INVALID_ADDRESS");
```

```solidity
contracts/token/VariableSupplyERC20Token.sol
27:         require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

37:         require(account != address(0), "INVALID_ADDRESS");

41:             require(amount <= mintableSupply, "INVALID_AMOUNT");
```

```solidity
contracts/token/FullPremintERC20Token.sol
11:         require(supply_ > 0, "NO_ZERO_MINT");
```

### Mitigation
Replace `require` statements with custom errors. Custom errors are defined using the `error` statement.

## [G-09] `++variable` costs less gas than `variable++`, especially when it’s used in for-loops (`--variable`/`variable--`too)
Prefix increments are cheaper than postfix increments.
Saves 6 gas *PER LOOP*
### Findings
```solidity
contracts/VTVLVesting.sol
353:         for (uint256 i = 0; i < length; i++) {
```
### Mitigation
Change `i++` to `++i`

## [G-10] Unchecked arithmetic
The default “checked” behavior costs more gas when adding/diving/multiplying, because under-the-hood those checks are implemented as a series of opcodes that, prior to performing the actual arithmetic, check for under/overflow and revert if it is detected.
if it can statically be determined there is no possible way for your arithmetic to under/overflow (such as a condition in an if statement), surrounding the arithmetic in an unchecked block will save gas.
### Findings
```solidity
contracts/VTVLVesting.sol
161:                 vestAmt += _claim.cliffAmount;
```
`vestAmt` is zero, can not overflow

```solidity
contracts/VTVLVesting.sol
292:         uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
```
can not overflow because of L256(it would revert here on overflow) :`require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");`

```solidity
contracts/VTVLVesting.sol
301:         numTokensReservedForVesting += allocatedAmount; // track the allocated amount
```
can not overflow because of L295(it would revert here on overflow) : ` require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");`

```solidity
contracts/VTVLVesting.sol
353:         for (uint256 i = 0; i < length; i++) {
```
unrealistic that admin would deal with arrays of length == type(uint256).max, so `i++` can be in `unchecked{}` block

```solidity
contracts/VTVLVesting.sol
381:         usrClaim.amountWithdrawn += amountRemaining;
```
will not overflow because `usrClaim.amountWithdrawn += amountRemaining  == allowance`

```solidity
contracts/VTVLVesting.sol
166:             if(_referenceTs > _claim.startTimestamp) {
167:                 uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
```
will not underflow because of L166

```solidity
contracts/VTVLVesting.sol
170:                 uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval
```
will not underflow because of L262: `require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP");`

```solidity
contracts/VTVLVesting.sol
217:         return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
```
will not underflow because of L187: `return (vestAmt > _claim.amountWithdrawn) ? vestAmt : _claim.amountWithdrawn;`

```solidity
contracts/VTVLVesting.sol
262:         require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp

264:         require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
```
`_endTimestamp - _startTimestamp` can not underflow because of L262

```solidity
contracts/VTVLVesting.sol
374:         require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

377:         uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
```
L377 can not underflow because of L374


```solidity
contracts/VTVLVesting.sol
383:         numTokensReservedForVesting -= amountRemaining;
```
can not underflow because `numTokensReservedForVesting > amountRemaining` always 

```solidity
contracts/VTVLVesting.sol
429:         uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
```
can not underflow because of L187: `return (vestAmt > _claim.amountWithdrawn) ? vestAmt : _claim.amountWithdrawn;`, so `finalVestAmt >= _claim.amountWithdrawn` always

```solidity
contracts/token/VariableSupplyERC20Token.sol
41:             require(amount <= mintableSupply, "INVALID_AMOUNT");

43:             mintableSupply -= amount;
```
L43 can not underflow because of L41

### Mitigation
Place the arithmetic operations in an `unchecked` block
```solidity
	for (uint i; i < length;) {
		...
		unchecked{ ++i; }
	}
```

## [G-11] Default value initialization
If a variable is not set/initialized, it is assumed to have the default value (`0`, `false`, `0x0` etc depending on the data type).
Explicitly initializing it with its default value is an anti-pattern and wastes gas.
### Findings
```solidity
contracts/VTVLVesting.sol
27:     uint112 public numTokensReservedForVesting = 0;
```

### Mitigation
Remove explicit initialization for default values.
```diff
contracts/VTVLVesting.sol
- 27:     uint112 public numTokensReservedForVesting = 0;
+ 27:     uint112 public numTokensReservedForVesting = 0;
```

## [G-12] Cache calculations
There is no need to calculate same thing two times
```solidity
contracts/VTVLVesting.sol
295:         require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

301:         numTokensReservedForVesting += allocatedAmount; // track the allocated amount
```
Cache result of `numTokensReservedForVesting + allocatedAmount` to save gas on additional SLOAD and checked arithmetic operation
### Mitigation
```diff
contracts/VTVLVesting.sol
+ uint112 newReservedAmount = numTokensReservedForVesting + allocatedAmount;
- 295:         require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
+ 295:         require(tokenAddress.balanceOf(address(this)) >= newReservedAmount, "INSUFFICIENT_BALANCE");

- 301:         numTokensReservedForVesting += allocatedAmount; // track the allocated amount
+ 301:         numTokensReservedForVesting = newReservedAmount; // track the allocated amount
```