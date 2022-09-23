# Summarize : I found 9 bugs 
# G-1 Using > 0 costs more gas than != 0 when used on a uint in a require() statement

[FullPremintERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11)
```
contracts/token/FullPremintERC20Token.sol:11:        require(supply_ > 0, "NO_ZERO_MINT");
```
[VariableSupplyERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27)
```
contracts/token/VariableSupplyERC20Token.sol:27:        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```
[VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol)
```
contracts/VTVLVesting.sol:107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
contracts/VTVLVesting.sol:256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
contracts/VTVLVesting.sol:257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
contracts/VTVLVesting.sol:263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
contracts/VTVLVesting.sol:272:                _cliffReleaseTimestamp > 0 && 
contracts/VTVLVesting.sol:273:                _cliffAmount > 0 && 
contracts/VTVLVesting.sol:449:        require(bal > 0, "INSUFFICIENT_BALANCE");
```

# G-02 Using of Pragma Solidity Version
 Remove pragma solidity version 
 
```
contracts/test/TestERC20Token.sol:2:pragma solidity ^0.8.0;
contracts/token/VariableSupplyERC20Token.sol:2:pragma solidity ^0.8.14;
```

# G-03. Pre-increment costs less gas as compared to Post-increment :
**Pre-increments and pre-decrements are cheaper.**

For a `uint256` i variable, the following is true with the Optimizer enabled at 10k:
Increment:
`i += 1` is the most expensive form
`i++` costs `6` `gas` less than `i += 1`
`++i` costs `5 gas` less than `i++` (11 gas less than i += 1)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name post-increment:


```
VTVLVesting.sol:353:        for (uint256 i = 0; i < length; i++) {
``` 


-----

# G-04. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas 
```
VTVLVesting.sol:353:        for (uint256 i = 0; i < length; i++) {
``` 
-----
# G-05. Splitting require() statements that use && saves gas

Require statements including conditions with the && operator can be broken down in multiple require statements to save gas.


```
VTVLVesting.sol:344:        require(_startTimestamps.length == length &&
``` 


-----

# G-06. Custom Errors instead of Revert Strings to save Gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met). Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.
Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).


```
AccessProtected.sol:25:        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
AccessProtected.sol:40:        require(admin != address(0), "INVALID_ADDRESS");
VTVLVesting.sol:82:        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
VTVLVesting.sol:107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
VTVLVesting.sol:111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
VTVLVesting.sol:129:        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
VTVLVesting.sol:255:        require(_recipient != address(0), "INVALID_ADDRESS");
VTVLVesting.sol:256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
VTVLVesting.sol:257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
VTVLVesting.sol:262:        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
VTVLVesting.sol:263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
VTVLVesting.sol:264:        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
VTVLVesting.sol:295:        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
VTVLVesting.sol:374:        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
VTVLVesting.sol:402:        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
VTVLVesting.sol:426:        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
VTVLVesting.sol:447:        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
VTVLVesting.sol:449:        require(bal > 0, "INSUFFICIENT_BALANCE");
token/VariableSupplyERC20Token.sol:27:        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
token/VariableSupplyERC20Token.sol:37:        require(account != address(0), "INVALID_ADDRESS");
token/VariableSupplyERC20Token.sol:41:            require(amount <= mintableSupply, "INVALID_AMOUNT");
token/FullPremintERC20Token.sol:11:        require(supply_ > 0, "NO_ZERO_MINT");
``` 



-----

# G-06. 10 ** 18 can be changed to 1e18 and save some gas


```
token/FullPremintERC20Token.sol:9:    // uint constant _initialSupply = 100 * (10**18);
``` 

### Recommendations:
10 ** 18 can be changed to 1e18 to avoid unnecessary arithmetic operation and save gas.

-----

## G-07. Boolean Comparision

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here

### Instances:

```
VTVLVesting.sol:111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
``` 



-----

# G-08. x += y costs more gas than x = x + y for state variables


```
VTVLVesting.sol:161:                vestAmt += _claim.cliffAmount;
VTVLVesting.sol:179:                vestAmt += linearVestAmount;
VTVLVesting.sol:301:        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
VTVLVesting.sol:381:        usrClaim.amountWithdrawn += amountRemaining;
VTVLVesting.sol:383:        numTokensReservedForVesting -= amountRemaining;
VTVLVesting.sol:433:        numTokensReservedForVesting -= amountRemaining; // Reduces the allocation
token/VariableSupplyERC20Token.sol:43:            mintableSupply -= amount;
``` 

-----

# G-09. Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint a;` instead of `uint a = 0;`

```
VTVLVesting.sol:27:    uint112 public numTokensReservedForVesting = 0;
VTVLVesting.sol:148:        uint112 vestAmt = 0;
``` 


-----
