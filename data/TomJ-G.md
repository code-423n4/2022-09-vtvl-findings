## Table of Contents
Total of 10 issues found.
- Should Use Unchecked Block where Over/Underflow is not Possible
- Defined Variables Used Only Once
- X = X + Y costs less gass than X += Y for state variables
- Use require instead of &&
- Change Function Visibility Public to External
- Use Calldata instead of Memory for Read Only Function Parameters
- Boolean Comparisons
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- ++i Costs Less Gas than i++
- Use Custom Errors to Save Gas

&ensp;
### Should Use Unchecked Block where Over/Underflow is not Possible

#### Issue
Since Solidity 0.8.0, all arithmetic operations revert on overflow and underflow by default.
However in places where overflow and underflow is not possible, it is better to use unchecked block to reduce the gas usage.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#checked-or-unchecked-arithmetic

#### PoC
Total of 7 instances found.

1. `_baseVestedAmount()` of `VTVLVesting.sol` (line 167)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L166-L167
```
Wrap line 167 with unchecked since underflow is not possible due to line 166 check
            if(_referenceTs > _claim.startTimestamp) {
                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
```

2. `_baseVestedAmount()` of `VTVLVesting.sol` (line 170)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L170
```
Wrap line 170 with unchecked since underflow is not possible due to line 262 check
262:        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
```
```
170:                uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval
```

3. `_createClaimUnchecked()` of `VTVLVesting.sol` (line 264)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L264
```
Wrap line 264 with unchecked since underflow is not possible due to line 262 check
262:        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
264:        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
```

4. `withdraw()` of `VTVLVesting.sol` (line 377)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374-L377
```
Wrap line 377 with unchecked since underflow is not possible due to line 374 check
374:        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
377:        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
```

5. `withdrawAdmin()` of `VTVLVesting.sol` (line 400)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L400
```
Wrap line 400 with unchecked since underflow is not possible due to line 295 check
400:        uint256 amountRemaining = tokenAddress.balanceOf(address(this)) - numTokensReservedForVesting;
```
```
295:        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
```

6. `revokeClaim()` of `VTVLVesting.sol` (line 429)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L426-L429
```
Wrap line 429 with unchecked since underflow is not possible due to line 426 check
426:        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
429:        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
```

7. `mint()` of `VariableSupplyERC20Token.sol` (line 43)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41-L43
```
Wrap line 43 with unchecked since underflow is not possible due to line 41 check
41:            require(amount <= mintableSupply, "INVALID_AMOUNT");
43:            mintableSupply -= amount;
```

#### Mitigation
Wrap the code with uncheck like described in above PoC. 

&ensp;
### Defined Variables Used Only Once

#### Issue
Certain variables is defined even though they are used only once.
Remove these unnecessary variables to save gas.
For cases where it will reduce the readability, one can use comments to help describe
what the code is doing.

#### PoC
Total of 2 instances found.

1. Remove '_claim' variable of `vestedAmount()` of `VTVLVesting.sol`
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L197-L198
Mitigation:
Delete line 197 and replace line 198 with below code
```
        return _baseVestedAmount(claims[_recipient], _referenceTs);
```

2. Remove 'amountRemaining' variable of `withdrawAdmin()` of `VTVLVesting.sol`
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L400-L402
Mitigation:
Delete line 400 and replace line 402 with below code
```
        require(tokenAddress.balanceOf(address(this)) - numTokensReservedForVesting >= _amountRequested, "INSUFFICIENT_BALANCE");
```

#### Mitigation
Don't define variable that is used only once.
Details are listed on above PoC.

&ensp;
### X = X + Y costs less gass than X += Y

#### Issue
X = X + Y costs less gass than X += Y for state variables

#### PoC
Total of 1 instance found.

1. `numTokensReservedForVesting` variable of `VTVLVesting.sol`
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L301
```
        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
```
Change it to
```
        numTokensReservedForVesting = numTokensReservedForVesting + allocatedAmount; // track the allocated amount
```
It saves 8 gas
Average gas before modificaiton: 167760
Average gas after modificaiton: 167752


&ensp;
### Use require instead of &&

#### Issue
When there are multiple conditions in require statement, break down the require statement into
multiple require statements instead of using && can save gas.

#### PoC
Total of 1 instance found.
```
./VTVLVesting.sol:344:        require(_startTimestamps.length == length &&
                                      _endTimestamps.length == length &&
                                      _cliffReleaseTimestamps.length == length &&
                                      _releaseIntervalsSecs.length == length &&
                                      _linearVestAmounts.length == length &&
                                      _cliffAmounts.length == length, 
                                      "ARRAY_LENGTH_MISMATCH"
```

#### Mitigation
Break down into several require statement.
For example,
```
require(_startTimestamps.length == length);
require(_endTimestamps.length == length);
require(_cliffreleasetimestamps.length == length);
require(_releaseIntervalsSecs.length == length);
require(_linearVestAmounts.length == length);
require(_cliffAmounts.length == length, "ARRAY_LENGTH_MISMATCH");
```

&ensp;
### Change Function Visibility Public to External

#### Issue
If the function is not called internally, it is cheaper to set your function visibility to external instead of public.

#### PoC
Total of 2 instances found.

1. VTVLVesting.sol:withdrawAdmin()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398

2. AccessProtected.sol:setAdmin()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39

#### Mitigation
Change the function visibility to external.

&ensp;
### Use Calldata instead of Memory for Read Only Function Parameters

#### Issue
It is cheaper gas to use calldata than memory if the function parameter is read only.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, 
and behaves mostly like memory. More details on following link.
link: https://docs.soliditylang.org/en/v0.8.15/types.html#data-location

#### PoC
Total of 7 instances found.
```
./VTVLVesting.sol:334:        address[] memory _recipients, 
./VTVLVesting.sol:335:        uint40[] memory _startTimestamps, 
./VTVLVesting.sol:336:        uint40[] memory _endTimestamps, 
./VTVLVesting.sol:337:        uint40[] memory _cliffReleaseTimestamps, 
./VTVLVesting.sol:338:        uint40[] memory _releaseIntervalsSecs, 
./VTVLVesting.sol:339:        uint112[] memory _linearVestAmounts, 
./VTVLVesting.sol:340:        uint112[] memory _cliffAmounts) 
```

#### Mitigation
Change memory to calldata

&ensp;
### Boolean Comparisons

#### Issue
It is more gas expensive to compare boolean with "variable == true" or "variable == false" than 
directly checking the returned boolean value.

#### PoC
Total of 1 instance found.
```
./VTVLVesting.sol:111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```

#### Mitigation
Simply check by returned boolean value.
Change it to
```
require(_claim.isActive, "NO_ACTIVE_CLAIM");
```

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size. Therefore it is more gas saving to use 32 bytes 
unless it is used in a struct or state variable in order to reduce storage slot. 

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 18 instances found.
```
./VTVLVesting.sol:27:    uint112 public numTokensReservedForVesting = 0;
./VTVLVesting.sol:64:    event Claimed(address indexed _recipient, uint112 _withdrawalAmount); 
./VTVLVesting.sol:74:    event AdminWithdrawn(address indexed _recipient, uint112 _amountRequested);
./VTVLVesting.sol:147:    function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
./VTVLVesting.sol:148:        uint112 vestAmt = 0;
./VTVLVesting.sol:167:                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
./VTVLVesting.sol:169:                uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
./VTVLVesting.sol:170:                uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval
./VTVLVesting.sol:176:                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;
./VTVLVesting.sol:196:    function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
./VTVLVesting.sol:206:    function finalVestedAmount(address _recipient) public view returns (uint112) {
./VTVLVesting.sol:215:    function claimableAmount(address _recipient) external view returns (uint112) {
./VTVLVesting.sol:292:        uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
./VTVLVesting.sol:371:        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
./VTVLVesting.sol:377:        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
./VTVLVesting.sol:398:    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    
./VTVLVesting.sol:422:        uint112 finalVestAmt = finalVestedAmount(_recipient);
./VTVLVesting.sol:429:        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
```

#### Mitigation
I suggest using uint256 instead of anything smaller and downcast where needed.

&ensp;
### ++i Costs Less Gas than i++

#### Issue
Prefix increments/decrements (++i or --i) costs cheaper gas than 
postfix increment/decrements (i++ or i--).

#### PoC
Total of 1 instance found.
```
./VTVLVesting.sol:353:        for (uint256 i = 0; i < length; i++) {
```

#### Mitigation
Change it to postfix increments/decrements.
It saves 6 gas per loop.
For example,
```
for (uint256 i = 0; i < length; ++i) {
```

&ensp;
### Use Custom Errors to Save Gas

#### Issue
Custom errors from Solidity 0.8.4 are cheaper than revert strings.
Details are explained here: https://blog.soliditylang.org/2021/04/21/custom-errors/

#### PoC
Total of 24 instances found.
```
./VTVLVesting.sol:82:        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
./VTVLVesting.sol:107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
./VTVLVesting.sol:111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
./VTVLVesting.sol:129:        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
./VTVLVesting.sol:255:        require(_recipient != address(0), "INVALID_ADDRESS");
./VTVLVesting.sol:256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
./VTVLVesting.sol:257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
./VTVLVesting.sol:262:        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
./VTVLVesting.sol:263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
./VTVLVesting.sol:264:        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
./VTVLVesting.sol:270:        require( 
                                  (
                                      _cliffReleaseTimestamp > 0 && 
                                      _cliffAmount > 0 && 
                                      _cliffReleaseTimestamp <= _startTimestamp
                                  ) || (
                                      _cliffReleaseTimestamp == 0 && 
                                      _cliffAmount == 0
                              ), "INVALID_CLIFF");
./VTVLVesting.sol:295:        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
./VTVLVesting.sol:344:        require(_startTimestamps.length == length &&
                                      _endTimestamps.length == length &&
                                      _cliffReleaseTimestamps.length == length &&
                                      _releaseIntervalsSecs.length == length &&
                                      _linearVestAmounts.length == length &&
                                      _cliffAmounts.length == length, 
                                      "ARRAY_LENGTH_MISMATCH"
                              );
./VTVLVesting.sol:374:        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
./VTVLVesting.sol:402:        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
./VTVLVesting.sol:426:        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
./VTVLVesting.sol:447:        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
./VTVLVesting.sol:449:        require(bal > 0, "INSUFFICIENT_BALANCE");
./FullPremintERC20Token.sol:11:        require(supply_ > 0, "NO_ZERO_MINT");
./VariableSupplyERC20Token.sol:27:        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
./VariableSupplyERC20Token.sol:37:        require(account != address(0), "INVALID_ADDRESS");
./VariableSupplyERC20Token.sol:41:            require(amount <= mintableSupply, "INVALID_AMOUNT");
./AccessProtected.sol:25:        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
./AccessProtected.sol:40:        require(admin != address(0), "INVALID_ADDRESS");
```

#### Mitigation
I suggest implementing custom errors to save gas.

&ensp;