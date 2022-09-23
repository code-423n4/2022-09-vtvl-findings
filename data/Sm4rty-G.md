

## G-01. Pre-increment costs less gas as compared to Post-increment :

++i costs less gas as compared to i++ for unsigned integer, as per-increment is cheaper(its about 5 gas per iteration cheaper)

i++ increments i and returns initial value of i. Which means

```
uint i =  1;
i++; // ==1 but i ==2
```

But ++i returns the actual incremented value:

```
uint i = 1;
++i; // ==2 and i ==2 , no need for temporary variable here
```

In the first case, the compiler has create a temporary variable (when used) for returning 1 instead of 2.

### Instances:

```
VTVLVesting.sol:353:        for (uint256 i = 0; i < length; i++) {
``` 

### Recommendations:
Change post-increment to pre-increment.

-----

## G-02. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

### Instances:

```
VTVLVesting.sol:353:        for (uint256 i = 0; i < length; i++) {
``` 

### Reference:

[https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops](https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops)

-----

## G-03. Using > 0 costs more gas than != 0 when used on a uint in a require() statement

> 0 is less efficient than != 0 for unsigned integers (with proof)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas) Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proof: https://twitter.com/gzeon/status/1485428085885640706

### Instances

```
VTVLVesting.sol:107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
VTVLVesting.sol:256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); 
VTVLVesting.sol:257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
VTVLVesting.sol:263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
VTVLVesting.sol:449:        require(bal > 0, "INSUFFICIENT_BALANCE");
token/VariableSupplyERC20Token.sol:27:        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
token/FullPremintERC20Token.sol:11:        require(supply_ > 0, "NO_ZERO_MINT");
``` 

### Reference:

[https://twitter.com/gzeon/status/1485428085885640706](https://twitter.com/gzeon/status/1485428085885640706)

### Remediation:

I suggest changing > 0 with != 0. Also, please enable the Optimizer.

-----


## G-04. Splitting require() statements that use && saves gas

Require statements including conditions with the && operator can be broken down in multiple require statements to save gas.

### Instances

```
VTVLVesting.sol:344:        require(_startTimestamps.length == length &&
``` 

### Mitigation:
Breakdown each condition in a separate require statement (though require statements should be replaced with custom errors)

-----

## G-05. Custom Errors instead of Revert Strings to save Gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met). Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.
Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

### Instances

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


### Remediation:
I suggest replacing revert strings with custom errors.

-----

## G-06. 10 ** 18 can be changed to 1e18 and save some gas

### Instances:

```
token/FullPremintERC20Token.sol:9:    // uint constant _initialSupply = 100 * (10**18);
``` 

### Recommendations:
10 ** 18 can be changed to 1e18 to avoid unnecessary arithmetic operation and save gas.

### References:

[https://github.com/code-423n4/2021-12-yetifinance-findings/issues/274](https://github.com/code-423n4/2021-12-yetifinance-findings/issues/274)

-----

## G-07. Boolean Comparision

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here

### Instances:

```
VTVLVesting.sol:111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
``` 
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-04-fundingonlywhenpricenotflagged-boolean-comparison-147](https://code4rena.com/reports/2022-04-badger-citadel/#g-04-fundingonlywhenpricenotflagged-boolean-comparison-147)


-----

## G-08. Strict inequalities (>) are more expensive than non-strict ones (>=)

Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas. I suggest using >= instead of > to avoid some opcodes here:

### Instances:

```
VTVLVesting.sol:187:        return (vestAmt > _claim.amountWithdrawn) ? vestAmt : _claim.amountWithdrawn;
``` 
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than](https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than)


-----

## G-09. x += y costs more gas than x = x + y for state variables

### Instances:

```
VTVLVesting.sol:161:                vestAmt += _claim.cliffAmount;
VTVLVesting.sol:179:                vestAmt += linearVestAmount;
VTVLVesting.sol:301:        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
VTVLVesting.sol:381:        usrClaim.amountWithdrawn += amountRemaining;
VTVLVesting.sol:383:        numTokensReservedForVesting -= amountRemaining;
VTVLVesting.sol:433:        numTokensReservedForVesting -= amountRemaining; // Reduces the allocation
token/VariableSupplyERC20Token.sol:43:            mintableSupply -= amount;
``` 
### References:

[https://github.com/code-423n4/2022-05-backd-findings/issues/108](https://github.com/code-423n4/2022-05-backd-findings/issues/108)


-----

## G-10. Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint number;` instead of `uint number = 0;`

### Instances:

```
VTVLVesting.sol:27:    uint112 public numTokensReservedForVesting = 0;
VTVLVesting.sol:148:        uint112 vestAmt = 0;
``` 
### Recommendation:

I suggest removing explicit initializations for default values.


-----

## G-11. Using bools for storage incurs overhead

```
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```

[Refer Here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Instances:

```
AccessProtected.sol:12:    mapping(address => bool) private _admins; // user address => admin? mapping
VTVLVesting.sol:45:        bool isActive; // whether this claim is active (or revoked)
``` 
### References:

[https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead](https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead)


-----


