# GAS
## Public function visibility can be made external
### Summary
Functions should have the strictest visibility possible. Public functions may lead to more gas usage by forcing the copy of their parameters to memory from calldata.
### Details
If a function is never called from the contract it should be marked as external. This will save gas.
### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L398-L411
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L39-L43
### Mitigation
Consider changing visibility from public to external.

## use of custom errors rather than revert() / require() error message
### Summary
Custom errors reduce 38 gas if the condition is met and 22 gas otherwise.
Also reduces contract size and deployment costs.
### Details
Since version 0.8.4 the use of custom errors rather than revert() / require() saves gas as noticed in
https://blog.soliditylang.org/2021/04/21/custom-errors/
https://github.com/code-423n4/2022-04-pooltogether-findings/issues/13

### Github Permalinks

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/FullPremintERC20Token.sol#L11
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L27
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L37
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L41
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L25
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L40
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L82
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L107
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L111
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L255
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L256
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L257
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L262
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L263
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L264
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L270
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L295
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L344
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L374
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L402
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L426
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L447
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L449
### Mitigation
replace each error message in a require by a custom error

## duplicated require() check should be refactored
### Summary
duplicated require() / revert() checks should be
refactored to a modifier or function to save gas
### Details
Event appears twice and can be reduced

- for example: 
```
    function enoughBalance(uint _amount, uint _amountRequested) public view{
               require(_amount >= _amountRequested, "INSUFFICIENT_BALANCE");
    }
```

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L295
        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L402
        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
- - - -
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L137
        // require(_claim.linearVestAmount + _claim.cliffAmount == 0, "CLAIM_ALREADY_EXISTS");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L138
        // require(_claim.amountWithdrawn == 0, "CLAIM_ALREADY_EXISTS");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L136
        // require(_claim.endTimestamp == 0, "CLAIM_ALREADY_EXISTS");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L129
        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
- - - -
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L40
        require(admin != address(0), "INVALID_ADDRESS");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L37
        require(account != address(0), "INVALID_ADDRESS");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L82
        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
### Mitigation
refactor this checks to different functions to save gas

## use != rather than >0 for unsigned integers in require() statements
### Summary
When the optimizer is enabled, gas is wasted by doing a greater-than operation, rather than a not-equals operation inside require() statements. When Using != , the optimizer is able to avoid the EQ, ISZERO, and associated operations, by relying on the JUMPI that comes afterwards, which itself checks for zero.
### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/FullPremintERC20Token.sol#L11
        require(supply_ > 0, "NO_ZERO_MINT");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L27
        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L107
        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L256
        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L257
        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L263
        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L272
                _cliffReleaseTimestamp > 0 && 
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L273
                _cliffAmount > 0 && 
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L449
        require(bal > 0, "INSUFFICIENT_BALANCE");
### Mitigation
Use != 0 rather than > 0 for unsigned integers in require()  statements.

## splitting require() statements that use && saves gas
### Summary
When the optimizer is enabled, gas is wasted by doing a greater-than operation, rather than a not-equals operation inside require() statements. When Using != , the optimizer is able to avoid the EQ, ISZERO, and associated operations, by relying on the JUMPI that comes afterwards, which itself checks for zero.
### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L272
                _cliffReleaseTimestamp > 0 && 

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L273
                _cliffAmount > 0 && 

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L276
                _cliffReleaseTimestamp == 0 && 

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L344
        require(_startTimestamps.length == length &&

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L345
                _endTimestamps.length == length &&

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L346
                _cliffReleaseTimestamps.length == length &&

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L347
                _releaseIntervalsSecs.length == length &&

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L348
                _linearVestAmounts.length == length &&

### Mitigation
Instead of using the && operator in a single require statement to check multiple conditions. Consider using multiple require statements with 1 condition per require statement (saving 3 gas per 
& ):

## usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
### Summary
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### Details
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 
Use a larger size than downcast where needed

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L27

### Mitigation
Consider using some data type that uses 32 bytes, for example uint256


## Explicit initialization
### Summary
It is not needed to initialize variables to the default value. Explicitly initializing it with its default value is an anti-pattern and wastes gas.
### Details
If a variable is not set/initialized, it is assumed to have the default value ( 0 for uint, false for bool, address(0) for address…). 

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L27
    uint112 public numTokensReservedForVesting = 0;

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L148
        uint112 vestAmt = 0;

### Mitigation
Don't initialize variables to default value

## Index initialized in for loop
### Summary
In for loops is not needed to initialize indexes to 0 as it is the default uint/int value. This saves gas.

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L353
        for (uint256 i = 0; i < length; i++) {

### Mitigation
Don't initialize variables to default value


## use of i++ in loop rather than ++i
### Summary
++i costs less gas than i++, especially when it's used in for loops
### Details
using ++i doesn't affect the flow of regular for loops and improves gas cost

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L353
        for (uint256 i = 0; i < length; i++) {

### Mitigation
Substitute to ++i 

## increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:

    for (uint256 i; i < numIterations; i++) {
    // ...
    }

    to

    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L353
        for (uint256 i = 0; i < length; i++) {
### Mitigation
Add unchecked ++i at the end of all the for loop where it's not expected to overflow and remove them from the for header


## Variables should be cached when used several times
### Summary
- Variables read more than once improves gas usage when cached into local variable
- Also functions which value is the same even called twice should be cached in a variable.
### Details
In loops or state variables, this is even more gas saving

### Github Permalinks
- msg.sender getter: `_msgSender()`
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L17-L18
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L367-L391
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L406-L410

- `mintableSupply`
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L40-L41

- `tokenAddress`
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L400-L407
### Mitigation
Cache variables used more than one into a local variable.

## Functions guaranteed to revert when called by normal users can be marked payable
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L36
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L39
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L325
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L341
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L398
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L418
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L446
### Mitigation
Consider adding payable to save gas

## >= cheaper than >
### Summary
Strict inequalities ( > ) are more expensive than non-strict ones ( >= ). This is due to some supplementary checks (ISZERO, 3 gas)

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L31
        if(initialSupply_ > 0) {

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L40
        if(mintableSupply > 0) {
### Mitigation
Consider using >= 1 instead of > 0 to avoid some opcodes


## <X> += <Y> costs more gas than <X> = <X> + <Y> for state variables
### Summary
x+=y costs more gas than x=x+y for state variables
Same applies for x-=y
### Github Permalinks
+=
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L301
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L381

-=
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L383
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L433
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L43
### Mitigation
Don't use += for state variables as it cost more gas.

## Use calldata instead of memory for function parameters
### Summary
It is generally cheaper to load variables directly from calldata, rather than copying them to memory. 
### Details
Only use memory if the variable needs to be modified
### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L334-L341

### Mitigation
Use calldata rather than memory in external functions where the parameter is not modified but only read

## Make constant state variables that do not change
### Summary
State variables which value isn't changed by any function in the contract, can be declared as a constant state variable to save some gas during deployment.

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L27

### Mitigation
- Add constant to state variables that do not change

## Make immutable state variables that do not change but assigned in the constructor
### Summary
State variables which value isn't changed by any function in the contract but constructor, can be declared as a immutable state variable to save some gas during deployment.

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L28
### Mitigation
- Add immutable to state variables that do not change but which value is assigned in constructor
