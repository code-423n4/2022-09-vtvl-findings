### G-01 Use multiple require rather than one require and && operator

I recomend to use multiple require instead of one require with `&&` operator. It will cost 8 gas for each `&&`.

_There have **2** istance of this issues:_

File: VTLVesting.sol from 270 till 278

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

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270

File: VTVLVesting.sol from 344 till 351

```
require(_startTimestamps.length == length &&
    _endTimestamps.length == length &&
    cliffReleaseTimestamps.length == length &&
    _releaseIntervalsSecs.length == length &&
    _linearVestAmounts.length == length &&
    _cliffAmounts.length == length,
    "ARRAY_LENGTH_MISMATCH"
    );
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344

### G-02 Using of custom errors than are came from 0.8.4 instead of `require()`/`revert()` strings will save deployment gas

Contracts use version 0.8.14 and can use custom errors that will save of deployment gas also when the trasaction failed duo to return the gas will be less.

_There have **23** istance of this issues:_

File: FullPremintERC20Token line 11

```
require(supply_ > 0, "NO_ZERO_MINT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11

File: VariableSupplyERC20Token lines 27, 37 41

```
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27

```
require(account != address(0), "INVALID_ADDRESS");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L37

```
require(amount <= mintableSupply, "INVALID_AMOUNT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41

File: AccessProtected line 40

```
require(admin != address(0), "INVALID_ADDRESS");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L40

File: VTVLVesting lines 82, 107, 111, 129, 255, 256, 257, 262, 263, 264, 270 till 278, 295, 344 till 351,374, 402, 426, 447, 449

```
require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L82

```
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107

```
require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111

```
require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L129

```
require(_recipient != address(0), "INVALID_ADDRESS");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255

```
require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256

```
require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L257

```
require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L262

```
require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263

```
require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L264

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

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270

```
require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L295

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

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344

```
require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374

```
require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L402

```
require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L426

```
require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L447

```
require(bal > 0, "INSUFFICIENT_BALANCE");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449

### G-03 Use `uncheck()` in for loops whenere overflow and undeflow is not possible

Using of `uncheck(i++)`/`uncheck(++i)` will save about 30 gas per iteration because compiler not save check everytime. This feature come from 0.8

_There have **1** istance of this issues:_

File: VTVLVesting line 353

```
for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353

### G-04 = + cost less than += for state variables

_There have **5** istance of this issues:_

File: VTVLVesting lines 161, 179, 381, 383

```
vestAmt += _claim.cliffAmount;
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L161

```
vestAmt += linearVestAmount;
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L179

```
usrClaim.amountWithdrawn += amountRemaining;
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L381

```
numTokensReservedForVesting -= amountRemaining;
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L383

```
numTokensReservedForVesting -= amountRemaining; // Reduces the allocation
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L433

### G-05 i++, i += 1 and i = i + 1 IN FOR LOOPS COSTS MORE GAS COMPARED TO ++i

++i will save about 5 gas for each iteration

_There have **1** istance of this issues:_

File: VTVLVesting line 353

```
for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353

### G-06 Recomend to use `uint256(1)`/`uint256(2)` for true and false

If you don't use boolean for storage you will avoid Gwarmaccess (100 gas). Also boolean from true to false cost 20000 gas rather than uint256(2) to uint256(1) that cost less.

_There have **2** istance of this issues:_

File: AccessProtected line 12

```
mapping(address => bool) private _admins; // user address => admin? mapping
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L12

File: VTVLVesting line 45

```
bool isActive; // whether this claim is active (or revoked)
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L45

### G-07 Recomend to use one operator for comparison

If you use `>=` or `<=` this will cost more gas because in the EVM there is no implementation of Opcodes for `>=` and `<= and two operations are done.

_There have **5** istance of this issues:_

File: VariableSupplyERC20Token line 41

```
require(amount <= mintableSupply, "INVALID_AMOUNT");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41

File: VTVLVesting line 160, 274, 295, 402

```
if(_referenceTs >= _claim.cliffReleaseTimestamp) {
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L160

```
_cliffReleaseTimestamp <= _startTimestamp
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L274

```
require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L295

```
require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L402

### G-08 `!=` cost 6 gas less than `>` in require

_There have **6** istance of this issues:_

File: VTVLVesting line 107, 263, 270 till 278, 374, 402, 449

```
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107

```
require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263

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

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270

```
require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374

```
require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L402

```
require(bal > 0, "INSUFFICIENT_BALANCE");
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449
