## Gas Optimizations
 *** 


### G-01: pre-increment `++i/--i` costs less gas than post-increment `i++/i--`
Saves 6 gas per loop in a for loop

Total instances of this issue: 1

instance #1
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353
```solidity
contracts/VTVLVesting.sol

353:        for (uint256 i = 0; i < length; i++) {

```

 *** 


### G-02: `++i/i++` should be placed in unchecked blocks to save gas as it is impossible for them to overflow in for and while loops
Unchecked keyword is available in solidity version `0.8.0` or higher and can be applied to iterator variables to save gas. 
Saves more than `30 gas` per loop.

Total instances of this issue: 1

instance #1
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353
```solidity
contracts/VTVLVesting.sol

353:        for (uint256 i = 0; i < length; i++) {

```

 *** 


### G-03: `x += y` costs more gas than `x = x + y` for state variables

Total instances of this issue: 3

instance #1
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L161
```solidity
contracts/VTVLVesting.sol

161:                vestAmt += _claim.cliffAmount;

```

instance #2
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L179
```solidity
contracts/VTVLVesting.sol

179:                vestAmt += linearVestAmount;

```

instance #3
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381
```solidity
contracts/VTVLVesting.sol

381:        usrClaim.amountWithdrawn += amountRemaining;

```

 *** 


### G-04: Put subtractions operation in `unchecked {}` block where the variable cannot underflow because of a previous `require()`
`require(a <= b); x = b - a` ==> `require(a <= b); unchecked { x = b - a }`

Total instances of this issue: 3

instance #1
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43
```solidity
contracts/token/VariableSupplyERC20Token.sol

43:            mintableSupply -= amount;

```

instance #2
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L377
```solidity
contracts/VTVLVesting.sol

377:        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;

```

instance #3
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L429
```solidity
contracts/VTVLVesting.sol

429:        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;

```

 *** 


### G-05: No need to compare boolean expressions with boolean literals, directly use the expression
if (<x> == true) ==> if (<x>)
if (<x> == false) => if (!<x>)

Total instances of this issue: 1

instance #1
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111
```solidity
contracts/VTVLVesting.sol

111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

```

 *** 


### G-06: Adding `payable` to functions which are only meant to be called by specific actors like `onlyOwner` will save gas cost
Marking functions payable removes additional checks for whether a payment was provided, hence reducing gas cost

Total instances of this issue: 7

instance #1
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36
```solidity
contracts/token/VariableSupplyERC20Token.sol

36:    function mint(address account, uint256 amount) public onlyAdmin {

```

instance #2
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39
```solidity
contracts/AccessProtected.sol

39:    function setAdmin(address admin, bool isEnabled) public onlyAdmin {

```

instance #3
Permalink: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L317-L325
```solidity
contracts/VTVLVesting.sol

317:    function createClaim(
318:            address _recipient, 
319:            uint40 _startTimestamp, 
320:            uint40 _endTimestamp, 
321:            uint40 _cliffReleaseTimestamp, 
322:            uint40 _releaseIntervalSecs, 
323:            uint112 _linearVestAmount, 
324:            uint112 _cliffAmount
325:                ) external onlyAdmin {

```

instance #4
Permalink: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L333-L341
```solidity
contracts/VTVLVesting.sol

333:    function createClaimsBatch(
334:        address[] memory _recipients, 
335:        uint40[] memory _startTimestamps, 
336:        uint40[] memory _endTimestamps, 
337:        uint40[] memory _cliffReleaseTimestamps, 
338:        uint40[] memory _releaseIntervalsSecs, 
339:        uint112[] memory _linearVestAmounts, 
340:        uint112[] memory _cliffAmounts) 
341:        external onlyAdmin {

```

instance #5
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398
```solidity
contracts/VTVLVesting.sol

398:    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    

```

instance #6
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L418
```solidity
contracts/VTVLVesting.sol

418:    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {

```

instance #7
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L446
```solidity
contracts/VTVLVesting.sol

446:    function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {

```

 *** 


### G-07: No need to initialize non-constant/non-immutable variables to zero
Since the default value is already zero, overwriting is not required.
Saves `8 gas` per instance.

Total instances of this issue: 3

instance #1
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
```solidity
contracts/VTVLVesting.sol

27:    uint112 public numTokensReservedForVesting = 0;

```

instance #2
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148
```solidity
contracts/VTVLVesting.sol

148:        uint112 vestAmt = 0;

```

instance #3
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353
```solidity
contracts/VTVLVesting.sol

353:        for (uint256 i = 0; i < length; i++) {

```

 *** 


### G-08: `require()` containing multiple checks joined with && should be broken into multiple require statements

Total instances of this issue: 2

instance #1
Permalink: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270-L278
```solidity
contracts/VTVLVesting.sol

270:        require( 
271:            (
272:                _cliffReleaseTimestamp > 0 && 
273:                _cliffAmount > 0 && 
274:                _cliffReleaseTimestamp <= _startTimestamp
275:            ) || (
276:                _cliffReleaseTimestamp == 0 && 
277:                _cliffAmount == 0
278:        ), "INVALID_CLIFF");

```

instance #2
Permalink: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L351
```solidity
contracts/VTVLVesting.sol

344:        require(_startTimestamps.length == length &&
345:                _endTimestamps.length == length &&
346:                _cliffReleaseTimestamps.length == length &&
347:                _releaseIntervalsSecs.length == length &&
348:                _linearVestAmounts.length == length &&
349:                _cliffAmounts.length == length, 
350:                "ARRAY_LENGTH_MISMATCH"
351:        );

```

 *** 


### G-09: Using uints/ints smaller than 256 bits increases overhead
Gas usage becomes higher with uint/int smaller than 256 bits because EVM operates on 32 bytes and uses additional operations to reduce the size from 32 bytes to the target size.

Total instances of this issue: 14

instance #1
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
```solidity
contracts/VTVLVesting.sol

27:    uint112 public numTokensReservedForVesting = 0;

```

instance #2
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L147
```solidity
contracts/VTVLVesting.sol

147:    function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {

```

instance #3
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148
```solidity
contracts/VTVLVesting.sol

148:        uint112 vestAmt = 0;

```

instance #4
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L169
```solidity
contracts/VTVLVesting.sol

169:                uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;

```

instance #5
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L176
```solidity
contracts/VTVLVesting.sol

176:                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;

```

instance #6
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L196
```solidity
contracts/VTVLVesting.sol

196:    function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {

```

instance #7
Permalink: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L245-L253
```solidity
contracts/VTVLVesting.sol

245:    function _createClaimUnchecked(
246:            address _recipient, 
247:            uint40 _startTimestamp, 
248:            uint40 _endTimestamp, 
249:            uint40 _cliffReleaseTimestamp, 
250:            uint40 _releaseIntervalSecs, 
251:            uint112 _linearVestAmount, 
252:            uint112 _cliffAmount
253:                ) private  hasNoClaim(_recipient) {

```

instance #8
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L292
```solidity
contracts/VTVLVesting.sol

292:        uint112 allocatedAmount = _cliffAmount + _linearVestAmount;

```

instance #9
Permalink: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L317-L325
```solidity
contracts/VTVLVesting.sol

317:    function createClaim(
318:            address _recipient, 
319:            uint40 _startTimestamp, 
320:            uint40 _endTimestamp, 
321:            uint40 _cliffReleaseTimestamp, 
322:            uint40 _releaseIntervalSecs, 
323:            uint112 _linearVestAmount, 
324:            uint112 _cliffAmount
325:                ) external onlyAdmin {

```

instance #10
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L371
```solidity
contracts/VTVLVesting.sol

371:        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));

```

instance #11
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L377
```solidity
contracts/VTVLVesting.sol

377:        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;

```

instance #12
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398
```solidity
contracts/VTVLVesting.sol

398:    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    

```

instance #13
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L422
```solidity
contracts/VTVLVesting.sol

422:        uint112 finalVestAmt = finalVestedAmount(_recipient);

```

instance #14
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L429
```solidity
contracts/VTVLVesting.sol

429:        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;

```

 *** 


### G-10: Using custom errors rather than revert()/require() strings will save deployment gas
Custom errors are available from solidity version 0.8.4.

Total instances of this issue: 21

instance #1
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11
```solidity
contracts/token/FullPremintERC20Token.sol

11:        require(supply_ > 0, "NO_ZERO_MINT");

```

instance #2
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27
```solidity
contracts/token/VariableSupplyERC20Token.sol

27:        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

```

instance #3
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L37
```solidity
contracts/token/VariableSupplyERC20Token.sol

37:        require(account != address(0), "INVALID_ADDRESS");

```

instance #4
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L41
```solidity
contracts/token/VariableSupplyERC20Token.sol

41:            require(amount <= mintableSupply, "INVALID_AMOUNT");

```

instance #5
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L25
```solidity
contracts/AccessProtected.sol

25:        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");

```

instance #6
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L40
```solidity
contracts/AccessProtected.sol

40:        require(admin != address(0), "INVALID_ADDRESS");

```

instance #7
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L82
```solidity
contracts/VTVLVesting.sol

82:        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");

```

instance #8
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107
```solidity
contracts/VTVLVesting.sol

107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

```

instance #9
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111
```solidity
contracts/VTVLVesting.sol

111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

```

instance #10
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L129
```solidity
contracts/VTVLVesting.sol

129:        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");

```

instance #11
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L255
```solidity
contracts/VTVLVesting.sol

255:        require(_recipient != address(0), "INVALID_ADDRESS");

```

instance #12
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257
```solidity
contracts/VTVLVesting.sol

257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

```

instance #13
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263
```solidity
contracts/VTVLVesting.sol

263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

```

instance #14
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L264
```solidity
contracts/VTVLVesting.sol

264:        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

```

instance #15
Permalink: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270-L278
```solidity
contracts/VTVLVesting.sol

270:        require( 
271:            (
272:                _cliffReleaseTimestamp > 0 && 
273:                _cliffAmount > 0 && 
274:                _cliffReleaseTimestamp <= _startTimestamp
275:            ) || (
276:                _cliffReleaseTimestamp == 0 && 
277:                _cliffAmount == 0
278:        ), "INVALID_CLIFF");

```

instance #16
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295
```solidity
contracts/VTVLVesting.sol

295:        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

```

instance #17
Permalink: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L351
```solidity
contracts/VTVLVesting.sol

344:        require(_startTimestamps.length == length &&
345:                _endTimestamps.length == length &&
346:                _cliffReleaseTimestamps.length == length &&
347:                _releaseIntervalsSecs.length == length &&
348:                _linearVestAmounts.length == length &&
349:                _cliffAmounts.length == length, 
350:                "ARRAY_LENGTH_MISMATCH"
351:        );

```

instance #18
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L374
```solidity
contracts/VTVLVesting.sol

374:        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

```

instance #19
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L402
```solidity
contracts/VTVLVesting.sol

402:        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");

```

instance #20
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426
```solidity
contracts/VTVLVesting.sol

426:        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

```

instance #21
Link: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449
```solidity
contracts/VTVLVesting.sol

449:        require(bal > 0, "INSUFFICIENT_BALANCE");

```

