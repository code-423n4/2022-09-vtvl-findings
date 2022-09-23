# QA Report

## Gas Optimizations
### 1. Custom Errors

Custom errors from Solidity 0.8.4 are cheaper than revert strings.

https://blog.soliditylang.org/2021/04/21/custom-errors/

Examples in contracts:

#### [FullPremintERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol)

[Line 11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11)
```
require(supply_ > 0, "NO_ZERO_MINT");
```
* * *
#### [VariableSupplyERC20Token](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol)

[Line 27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27)
```
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```
* * *
[Line 37](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L37)
```
require(account != address(0), "INVALID_ADDRESS");
```
* * *
[Line 41](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41)
```
require(amount <= mintableSupply, "INVALID_AMOUNT");
```
* * *
### [AccessProtected.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol)

[Line 25](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L25)
```
require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
```
* * *
[Line 40](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L40)
```
require(admin != address(0), "INVALID_ADDRESS");
```
* * *
### [VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol)

[Line 82](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L82)
```
require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
```
* * *
[Line 107](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107)
```
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```
* * *
[Line 111](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111)
```
require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```
* * *
[Line 129](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L129)
```
require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
```
* * *
[Line 255-257](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255-L257)

```
require(_recipient != address(0), "INVALID_ADDRESS");
require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
```

* * *
[Line 262-264](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L262-L264)
```
require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
```
* * *
[Line 270-278](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270-L278)
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
* * *
[Line 295](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L295)
```
require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
```
* * *
[Line 344-351](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L351)
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
* * *
[Line 374](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374)
```
require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
```
* * *
[Line 402](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L402)
```
require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
```
* * *
[Line 426](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L426)
```
require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
```
* * *
[Line 447](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L447)
```
require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
```
* * *
[Line 449](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449)
```
require(bal > 0, "INSUFFICIENT_BALANCE");
```

* * *

### 2. != 0 is more efficient then > 0 for uints

!= 0 costs less gas compared to > 0 for unsigned integer.

Examples in contracts:

#### [FullPremintERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol)

[Line 11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11)
```
require(supply_ > 0, "NO_ZERO_MINT");
```
* * *
#### [VariableSupplyERC20Token](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol)
[Line 27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27)
```
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```
* * *
[Line 31](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L31)
```
if(initialSupply_ > 0) {
```
* * *
[Line 40](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L40)
```
if(mintableSupply > 0) {
```
* * *
### [VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol)
[Line 107](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107)
```
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```
* * *
[Line 256-257](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256-L257)
```
require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
```
* * *
[Line 263](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263)
```
require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
```
* * *
[Line 270-278](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270-L278)
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
* * *
[Line 449](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449)
```
require(bal > 0, "INSUFFICIENT_BALANCE");
```
* * *

### 3. Unnecessary Default Value Initialization
When a variable is not initialized, it will have its default values.
For example, 0 for uint, false for bool and address(0) for address.

```
uint a = 0;
```
to
```
uint a;
```

Examples in contracts:

### [VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol)
[Line 27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27)
```
uint112 public numTokensReservedForVesting = 0;
```
* * *
[Line 148](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148)
```
uint112 vestAmt = 0;
```
* * *
[Line 353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)
```
for (uint256 i = 0; i < length; i++) {
```
***

### 4. ++i is more efficient then i++
```
for(...; i++)
```
to
```
for(...; ++i)
```
Examples in contracts:
### [VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol)
[Line 353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)
```
for (uint256 i = 0; i < length; i++) {
```
* * *

### 5. Splitting require statements

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save gas.
```
require(a && b);
```
to
```
require(a);
require(b);
```

Examples in code: 
[Line 344-351](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L351)
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
* * *

### 6. Increments can be unchecked
In Solidity 0.8+, there’s a default overflow check on unsigned integers. you can uncheck this in for-loops and save some gas.

```
for (uint256 i; i < length; ++i) {  
 // ...  
}  
```
to
```
for (uint256 i; i < length;) {  
 // ...  
 unchecked { ++i; }  
}  
```

Examples in code: 

[Line 353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)

```
for (uint256 i = 0; i < length; i++) {
```
* * *