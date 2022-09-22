# Gas Optimization


## ✅ G-1: Don't Initialize Variables with Default Value

### 📝 Description
Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas. Not overwriting the default for [stack variables](https://gist.github.com/IllIllI000/e075d189c1b23dce256cd166e28f3397) saves 8 gas. Storage and memory variables have larger savings

### 💡 Recommendation
Delete useless variable declarations to save gas.

### 🔍 Findings:
```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148``` [uint112 vestAmt = 0;](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353``` [for (uint256 i = 0; i < length; i++) {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353 )

## ✅ G-2: Use != 0 instead of > 0 for Unsigned Integer Comparison

### 📝 Description
Use != 0 when comparing uint variables to zero, which cannot hold values below zero

### 💡 Recommendation
You should change from `> 0` to  `!=0`.

### 🔍 Findings:
```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107``` [require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256``` [require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257``` [require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263``` [require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272``` [_cliffReleaseTimestamp > 0 &&](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L273``` [_cliffAmount > 0 &&](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L273 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449``` [require(bal > 0, "INSUFFICIENT_BALANCE");](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449 )

```2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11``` [require(supply_ > 0, "NO_ZERO_MINT");](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11 )

```2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27``` [require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27 )

```2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31``` [if(initialSupply_ > 0) {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31 )

```2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40``` [if(mintableSupply > 0) {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40 )
## ✅ G-3: Functions guaranteed to revert when called by normal users can be marked payable

### 📝 Description
See [this issue](https://github.com/code-423n4/2022-06-putty-findings/issues/59) 
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost 
Saves 2400 gas per instance in deploying contracrt.
Saves about 20 gas per instance if using assembly to executing the function.

### 💡 Recommendation
You should add the keyword `payable` to those functions.

### 🔍 Findings:

```2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39``` [function setAdmin(address admin, bool isEnabled) public onlyAdmin {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256``` [require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L325``` [) external onlyAdmin {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L325 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L341``` [external onlyAdmin {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L341 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398``` [function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L418``` [function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L418 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L446``` [function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L446 )

```2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36``` [function mint(address account, uint256 amount) public onlyAdmin {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36 )

## ✅ G-4: Splitting require() statements that use && saves gas

### 📝 Description
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

### 💡 Recommendation
You should change one require which has `&&` to two simple require() statements to save gas

### 🔍 Findings:
```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272``` [_cliffReleaseTimestamp > 0 &&](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L273``` [_cliffAmount > 0 &&](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L273 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L276``` [_cliffReleaseTimestamp == 0 &&](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L276 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344``` [require(_startTimestamps.length == length &&](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L345``` [_endTimestamps.length == length &&](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L345 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L346``` [_cliffReleaseTimestamps.length == length &&](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L346 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L347``` [_releaseIntervalsSecs.length == length &&](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L347 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L348``` [_linearVestAmounts.length == length &&](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L348 )


## ✅ G-5: Use `++i` instead of `i++`

### 📝 Description
You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting
The post-increment operation (i++) boils down to saving the original value of i, incrementing it and saving that to a temporary place in memory, and then returning the original value; only after that value is returned is the value of i actually updated (4 operations).On the other hand, in pre-increment doesn't need to store temporary(2 operations) so, the pre-increment operation uses less opcodes and is thus more gas efficient.

### 💡 Recommendation
You should change from i++ to ++i.

### 🔍 Findings:
```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353``` [for (uint256 i = 0; i < length; i++) {](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353 )


## ✅ G-6: Use x=x+y instad of x+=y

### 📝 Description
You can save about 35 gas per instance if you change from `x+=y**`** to `x=x+y`
This works equally well for subtraction, multiplication and division.

### 💡 Recommendation
You should change from `x+=y` to `x=x+y`.

### 🔍 Findings:
```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L161``` [vestAmt += _claim.cliffAmount;](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L161 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L179``` [vestAmt += linearVestAmount;](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L179 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301``` [numTokensReservedForVesting += allocatedAmount; // track the allocated amount](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381``` [usrClaim.amountWithdrawn += amountRemaining;](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L383``` [numTokensReservedForVesting -= amountRemaining;](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L383 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L433``` [numTokensReservedForVesting -= amountRemaining; // Reduces the allocation](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L433 )

```2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43``` [mintableSupply -= amount;](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43 )


## ✅ G-7: Use `indexed` for uint, bool, and address

### 📝 Description
Using the indexed keyword for value types such as uint, bool, and address saves gas costs, as seen in the example below. However, this is only the case for value types, whereas indexing bytes and strings are more expensive than their unindexed version.
Also indexed keyword has more merits.
It can be useful to have a way to monitor the contract's activity after it was deployed. One way to accomplish this is to look at all transactions of the contract, however that may be insufficient, as message calls between contracts are not recorded in the blockchain. Moreover, it shows only the input parameters, not the actual changes being made to the state. Also events could be used to trigger functions in the user interface.

### 💡 Recommendation
Up to three `indexed` can be used per event and should be added.

### 🔍 Findings:

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L64``` [event Claimed(address indexed _recipient, uint112 _withdrawalAmount);](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L64 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L69``` [event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L69 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L74``` [event AdminWithdrawn(address indexed _recipient, uint112 _amountRequested);](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L74 )
