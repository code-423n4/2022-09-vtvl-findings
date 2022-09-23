## [G-1] Use preincrement to save gas
```solidity
VTVLVesting.sol:353:        for (uint256 i = 0; i < length; i++) {
```
## [G-2] Splitting `require()` statements that use `&&` saves gas

If you're using the Optimizer at 200, instead of using the `&&` operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement:
```solidity
VTVLVesting.sol:343:        uint256 length = _recipients.length;
VTVLVesting.sol:344:        require(_startTimestamps.length == length &&
VTVLVesting.sol:345:                _endTimestamps.length == length &&
VTVLVesting.sol:346:                _cliffReleaseTimestamps.length == length &&
VTVLVesting.sol:347:                _releaseIntervalsSecs.length == length &&
VTVLVesting.sol:348:                _linearVestAmounts.length == length &&
VTVLVesting.sol:349:                _cliffAmounts.length == length, 
```

## [G-3] Donot use default values Explicit initialization with zero is not required for variable declaration of numTokens because `uints are 0` by default.removeing this will reduce contract size and save a bit of gas.

```solidity
VTVLVesting.sol:148:        uint112 vestAmt = 0;
VTVLVesting.sol:353:        for (uint256 i = 0; i < length; i++) {
```

## [G-4] `public` functions not called by the contract should be declared `external` instead   
```solidity
VTVLVesting.sol:398:    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    
```

## [G-5] variable == false|0 -> !variable or variable ==  true -> variable
```solidity
VTVLVesting.sol:111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
VTVLVesting.sol:129:        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
VTVLVesting.sol:264:        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
VTVLVesting.sol:276:                _cliffReleaseTimestamp == 0 && 
VTVLVesting.sol:277:                _cliffAmount == 0
```
## [G-06] Use `!= 0` instead of `> 0` in HolyPaladinToken.sol
```solidity
token/VariableSupplyERC20Token.sol:27:        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
token/VariableSupplyERC20Token.sol:31:        if(initialSupply_ > 0) {
token/VariableSupplyERC20Token.sol:40:        if(mintableSupply > 0) {
token/FullPremintERC20Token.sol:11:        require(supply_ > 0, "NO_ZERO_MINT");
VTVLVesting.sol:107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
VTVLVesting.sol:256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
VTVLVesting.sol:257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
VTVLVesting.sol:263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
VTVLVesting.sol:272:                _cliffReleaseTimestamp > 0 && 
VTVLVesting.sol:273:                _cliffAmount > 0 && 
VTVLVesting.sol:449:        require(bal > 0, "INSUFFICIENT_BALANCE");
```
## [G-7] <x> += <y>` costs more gas than `<x> = <x> + <y>` 
```solidity
token/VariableSupplyERC20Token.sol:43:            mintableSupply -= amount;
VTVLVesting.sol:161:                vestAmt += _claim.cliffAmount;
VTVLVesting.sol:179:                vestAmt += linearVestAmount;
VTVLVesting.sol:301:        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
VTVLVesting.sol:381:        usrClaim.amountWithdrawn += amountRemaining;
VTVLVesting.sol:383:        numTokensReservedForVesting -= amountRemaining;
VTVLVesting.sol:433:        numTokensReservedForVesting -= amountRemaining; // Reduces the allocation
```
## [G-8] Add a check if `new admin` is already a `admin` or the `one to be removed` is already
`removed or non exist` this will save gas by avoiding rewrite of same data
```solidity
AccessProtected.sol:39: function setAdmin(address admin, bool isEnabled) public onlyAdmin {
AccessProtected.sol:40:         require(admin != address(0), "INVALID_ADDRESS");
AccessProtected.sol:41:         _admins[admin] = isEnabled;
AccessProtected.sol:42:         emit AdminAccessSet(admin, isEnabled);
AccessProtected.sol:43:     }

```