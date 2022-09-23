### G-01 USING `> 0` COSTS MORE GAS THAN `!= 0` WHEN USED ON A `UINT` IN A `REQUIRE()` STATEMENT

*Total Instances Identified: 9*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11

```
require(supply_ > 0, "NO_ZERO_MINT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27

```
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
107: require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

256: require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");

257: require(_startTimestamp > 0, "INVALID_START_TIMESTAMP")

263: require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

272: _cliffReleaseTimestamp > 0

273: _cliffAmount > 0

449: require(bal > 0, "INSUFFICIENT_BALANCE");

```


### G-02 USE CUSTOM ERRORS RATHER THAN REVERT() or REQUIRE() STRINGS TO SAVE GAS

*Total Instances Identified: 24*

Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they’re hitby [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas


https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol

```
11: require(supply_ > 0, "NO_ZERO_MINT");
```


https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

```
27: require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

37: require(account != address(0), "INVALID_ADDRESS");

41: require(amount <= mintableSupply, "INVALID_AMOUNT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol

```
25: require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");

40: require(admin != address(0), "INVALID_ADDRESS");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
82: require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");

107: require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

111: require(_claim.isActive == true, "NO_ACTIVE_CLAIM")

129: require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");

255: require(_recipient != address(0), "INVALID_ADDRESS");

256: require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");

257: require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

262: require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP");

263: require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

264: require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

295: require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

344: require(....

374: require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

402: require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");

426: require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

447: require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN");

449: require(bal > 0, "INSUFFICIENT_BALANCE");
```


### G-03 UNCHECKING ARITHMETICS OPERATIONS THAT CANT UNDERFLOW or OVERFLOW

*Total Instances Identified: 19*

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block: [Solidity Doc](https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic)

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

```
43:  mintableSupply -= amount;
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
161: vestAmt += _claim.cliffAmount;

167: uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp;

169: uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;

170: uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp;

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


### G-04 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Total Instances Identified: 1*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

```
for (uint256 i = 0; i < length; i++)
```


### G-05 DONT COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

*Total Instances Identified: 1*

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111

```
require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```


### G-06 Using `>=` to COSTS LESS GAS THAN `>`

*Total Instances Identified: 4*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
154: if(_referenceTs > _claim.endTimestamp) {

166: if(_referenceTs > _claim.startTimestamp) {

187: return (vestAmt > _claim.amountWithdrawn) ? vestAmt : _claim.amountWithdrawn;

374: require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
```


### G-07 IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

*Total Instances Identified: 3*

If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
27: uint112 public numTokensReservedForVesting = 0;

148: uint112 vestAmt = 0;

353: for (uint256 i = 0; i < length; i++)
```


### G-08 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Total Instances Identified: 7*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
161: vestAmt += _claim.cliffAmount;

179: vestAmt += linearVestAmount;

301: numTokensReservedForVesting += allocatedAmount;

381: usrClaim.amountWithdrawn += amountRemaining;

383: numTokensReservedForVesting -= amountRemaining;

433: numTokensReservedForVesting -= amountRemaining;
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43

```
mintableSupply -= amount;
```

### G-09 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

*Total Instances Identified: 46*

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

**Contract :** https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol


### G-10 USE MSG.SENDER INSTEAD OF OPENZEPPELINS \_MSGSENDER() WHEN META-TRANSACTIONS CAPABILITIES AREN’T USED

*Total Instances Identified: 13*

`msg.sender` costs 2 gas (CALLER opcode). `_msgSender()` represents the following:

```
function _msgSender() internal view virtual returns (address payable) {  return msg.sender;}
```

When no meta-transactions capabilities are used: `msg.sender` is enough.

See [https://docs.openzeppelin.com/contracts/2.x/gsn](https://docs.openzeppelin.com/contracts/2.x/gsn) for more information about GSN capabilities.

Consider replacing `_msgSender()` with `msg.sender` here:

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol

```
17:  _admins[_msgSender()] = true;

18: emit AdminAccessSet(_msgSender(), true);

25: require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
364: function withdraw() hasActiveClaim(_msgSender()) externa

367: Claim storage usrClaim = claims[_msgSender()];

371: uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));

387: tokenAddress.safeTransfer(_msgSender(), amountRemaining);

390: emit Claimed(_msgSender(), amountRemaining);

406: tokenAddress.safeTransfer(_msgSender(), _amountRequested);

409:  emit AdminWithdrawn(_msgSender(), _amountRequested);

449: _otherTokenAddress.safeTransfer(_msgSender(), bal);
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L12

```
_mint(_msgSender(), supply_);
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L32

```
mint(_msgSender(), initialSupply_);
```


### G-11 FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

*Total Instances Identified: 7*

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2), `DUP1`(3), `ISZERO`(3), `PUSH2`(3), `JUMPI`(10), `PUSH1`(3), `DUP1`(3), `REVERT`(0), `JUMPDEST`(1), `POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39

```
function setAdmin(address admin, bool isEnabled) public onlyAdmin
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36

```
function mint(address account, uint256 amount) public onlyAdmin
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
317: function createClaim(...) external onlyAdmin

333: function createClaimsBatch(...) external onlyAdmin

397: function withdrawAdmin(uint112 _amountRequested) public onlyAdmin

417: function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient)

445: function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin
```

### G-12 USING BOOLS FOR STORAGE INCURS OVERHEAD

*Total Instances Identified: 3*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
288: isActive: true

431:  _claim.isActive = false;
```


https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L17

```
_admins[_msgSender()] = true;
```


### G-13 SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

*Total Instances Identified: 2*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270-L278

```
        require( 
            (
                _cliffReleaseTimestamp > 0 &&  
                _cliffAmount > 0 && 
                _cliffReleaseTimestamp <= _startTimestamp
            ) || (
                _cliffReleaseTimestamp == 0 && 
                _cliffAmount == 0
        ), "INVALID_CLIFF");
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L351

```
        require(_startTimestamps.length == length &&
                _endTimestamps.length == length &&
                _cliffReleaseTimestamps.length == length &&
                _releaseIntervalsSecs.length == length &&
                _linearVestAmounts.length == length &&
                _cliffAmounts.length == length, 
                "ARRAY_LENGTH_MISMATCH"
        );
```


### G-14 ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 (SAME FOR --I VS I-- OR I -= 1)

*Total Instances Identified: 1*

Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

-   `i += 1` is the most expensive form
-   `i++` costs 6 gas less than `i += 1`
-   `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

-   `i -= 1` is the most expensive form
-   `i--` costs 11 gas less than `i -= 1`
-   `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

```
- 353: for (uint256 i = 0; i < length; i++) {
+ 353: for (uint256 i = 0; i < length; ++i) {
```

