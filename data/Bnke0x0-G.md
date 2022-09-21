

## Issues found

### [G01] State variables only set in the constructor should be declared `immutable`


#### Findings:
```
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::11 => uint256 public mintableSupply;
```



### [G02] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```



### [G03] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```


### [G04] Not using the named return variables when a function returns, wastes deployment gas

#### Findings:
```
2022-09-vtvl/contracts/AccessProtected.sol::30 => return _admins[_addressToCheck];
2022-09-vtvl/contracts/VTVLVesting.sol::198 => return _baseVestedAmount(_claim, _referenceTs);
2022-09-vtvl/contracts/VTVLVesting.sol::208 => return _baseVestedAmount(_claim, _claim.endTimestamp);
2022-09-vtvl/contracts/VTVLVesting.sol::217 => return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
```



### [G05] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::107 => require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
2022-09-vtvl/contracts/VTVLVesting.sol::256 => require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
2022-09-vtvl/contracts/VTVLVesting.sol::257 => require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
2022-09-vtvl/contracts/VTVLVesting.sol::263 => require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
2022-09-vtvl/contracts/VTVLVesting.sol::449 => require(bal > 0, "INSUFFICIENT_BALANCE");
2022-09-vtvl/contracts/token/FullPremintERC20Token.sol::11 => require(supply_ > 0, "NO_ZERO_MINT");
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::27 => require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```



### [G06] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::27 => uint112 public numTokensReservedForVesting = 0;
2022-09-vtvl/contracts/VTVLVesting.sol::148 => uint112 vestAmt = 0;
2022-09-vtvl/contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```



### [G07] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)


#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::353 => for (uint256 i = 0; i < length; i++) {
```



### [G08] Splitting `require()` statements that use `&&` Cost gas

#### Impact
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) for an example
#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::344 => require(_startTimestamps.length == length &&
```



### [G09] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::27 => uint112 public numTokensReservedForVesting = 0;
2022-09-vtvl/contracts/VTVLVesting.sol::35 => uint40 startTimestamp; // When does the vesting start (40 bits is enough for TS)
2022-09-vtvl/contracts/VTVLVesting.sol::36 => uint40 endTimestamp; // When does the vesting end - the vesting goes linearly between the start and end timestamps
2022-09-vtvl/contracts/VTVLVesting.sol::37 => uint40 cliffReleaseTimestamp; // At which timestamp is the cliffAmount released. This must be <= startTimestamp
2022-09-vtvl/contracts/VTVLVesting.sol::38 => uint40 releaseIntervalSecs; // Every how many seconds does the vested amount increase.
2022-09-vtvl/contracts/VTVLVesting.sol::42 => uint112 linearVestAmount; // total entitlement
2022-09-vtvl/contracts/VTVLVesting.sol::43 => uint112 cliffAmount; // how much is released at the cliff
2022-09-vtvl/contracts/VTVLVesting.sol::44 => uint112 amountWithdrawn; // how much was withdrawn thus far - released at the cliffReleaseTimestamp
2022-09-vtvl/contracts/VTVLVesting.sol::64 => event Claimed(address indexed _recipient, uint112 _withdrawalAmount);
2022-09-vtvl/contracts/VTVLVesting.sol::69 => event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
2022-09-vtvl/contracts/VTVLVesting.sol::74 => event AdminWithdrawn(address indexed _recipient, uint112 _amountRequested);
2022-09-vtvl/contracts/VTVLVesting.sol::147 => function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
2022-09-vtvl/contracts/VTVLVesting.sol::148 => uint112 vestAmt = 0;
2022-09-vtvl/contracts/VTVLVesting.sol::169 => uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
2022-09-vtvl/contracts/VTVLVesting.sol::176 => uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;
2022-09-vtvl/contracts/VTVLVesting.sol::196 => function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
2022-09-vtvl/contracts/VTVLVesting.sol::206 => function finalVestedAmount(address _recipient) public view returns (uint112) {
2022-09-vtvl/contracts/VTVLVesting.sol::215 => function claimableAmount(address _recipient) external view returns (uint112) {
2022-09-vtvl/contracts/VTVLVesting.sol::217 => return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
2022-09-vtvl/contracts/VTVLVesting.sol::247 => uint40 _startTimestamp,
2022-09-vtvl/contracts/VTVLVesting.sol::248 => uint40 _endTimestamp,
2022-09-vtvl/contracts/VTVLVesting.sol::249 => uint40 _cliffReleaseTimestamp,
2022-09-vtvl/contracts/VTVLVesting.sol::250 => uint40 _releaseIntervalSecs,
2022-09-vtvl/contracts/VTVLVesting.sol::251 => uint112 _linearVestAmount,
2022-09-vtvl/contracts/VTVLVesting.sol::252 => uint112 _cliffAmount
2022-09-vtvl/contracts/VTVLVesting.sol::292 => uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
2022-09-vtvl/contracts/VTVLVesting.sol::319 => uint40 _startTimestamp,
2022-09-vtvl/contracts/VTVLVesting.sol::320 => uint40 _endTimestamp,
2022-09-vtvl/contracts/VTVLVesting.sol::321 => uint40 _cliffReleaseTimestamp,
2022-09-vtvl/contracts/VTVLVesting.sol::322 => uint40 _releaseIntervalSecs,
2022-09-vtvl/contracts/VTVLVesting.sol::323 => uint112 _linearVestAmount,
2022-09-vtvl/contracts/VTVLVesting.sol::324 => uint112 _cliffAmount
2022-09-vtvl/contracts/VTVLVesting.sol::335 => uint40[] memory _startTimestamps,
2022-09-vtvl/contracts/VTVLVesting.sol::336 => uint40[] memory _endTimestamps,
2022-09-vtvl/contracts/VTVLVesting.sol::337 => uint40[] memory _cliffReleaseTimestamps,
2022-09-vtvl/contracts/VTVLVesting.sol::338 => uint40[] memory _releaseIntervalsSecs,
2022-09-vtvl/contracts/VTVLVesting.sol::339 => uint112[] memory _linearVestAmounts,
2022-09-vtvl/contracts/VTVLVesting.sol::340 => uint112[] memory _cliffAmounts)
2022-09-vtvl/contracts/VTVLVesting.sol::371 => uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
2022-09-vtvl/contracts/VTVLVesting.sol::377 => uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
2022-09-vtvl/contracts/VTVLVesting.sol::398 => function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
2022-09-vtvl/contracts/VTVLVesting.sol::422 => uint112 finalVestAmt = finalVestedAmount(_recipient);
2022-09-vtvl/contracts/VTVLVesting.sol::429 => uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
2022-09-vtvl/contracts/VTVLVesting.sol::436 => emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);
```


### [G10] `require()` or `revert()` statements that check input arguments should be at the top of the function



#### Findings:
```
2022-09-vtvl/contracts/AccessProtected.sol::40 => require(admin != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/VTVLVesting.sol::82 => require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/VTVLVesting.sol::255 => require(_recipient != address(0), "INVALID_ADDRESS");
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::37 => require(account != address(0), "INVALID_ADDRESS");
```






### [G11] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2022-09-vtvl/contracts/AccessProtected.sol::39 => function setAdmin(address admin, bool isEnabled) public onlyAdmin {
2022-09-vtvl/contracts/VTVLVesting.sol::398 => function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
2022-09-vtvl/contracts/VTVLVesting.sol::418 => function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {
2022-09-vtvl/contracts/VTVLVesting.sol::446 => function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::36 => function mint(address account, uint256 amount) public onlyAdmin {
```



### [G12] Use a more recent version of solidity



#### Findings:
```
2022-09-vtvl/contracts/AccessProtected.sol::2 => pragma solidity 0.8.14;
2022-09-vtvl/contracts/VTVLVesting.sol::2 => pragma solidity 0.8.14;
2022-09-vtvl/contracts/token/FullPremintERC20Token.sol::2 => pragma solidity 0.8.14;
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::2 => pragma solidity ^0.8.14;
```


### [G13] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.

There are several cases of function arguments using memory instead of calldata
#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::147 => function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
```



### [G14] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done at some places, it’s not consistently done in the solution.

I suggest adding a non-zero-value check here

#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::388 => tokenAddress.safeTransfer(_msgSender(), amountRemaining);
2022-09-vtvl/contracts/VTVLVesting.sol::407 => tokenAddress.safeTransfer(_msgSender(), _amountRequested);
2022-09-vtvl/contracts/VTVLVesting.sol::450 => _otherTokenAddress.safeTransfer(_msgSender(), bal);
```





### [G15] Using `bools` for storage incurs overhead

#### Impact

#### Findings:
```
2022-09-vtvl/contracts/AccessProtected.sol::12 => mapping(address => bool) private _admins; // user address => admin? mapping
2022-09-vtvl/contracts/VTVLVesting.sol::45 => bool isActive; // whether this claim is active (or revoked)
```




### [G16] Don’t compare boolean expressions to boolean literals

#### Impact
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)
#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::111 => require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```



### [G17] Optimize names to save gas

#### Impact
public/external function names and public member variable names can be optimized to save gas. See this
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per sorted position shifted

#### Findings:
```
2022-09-vtvl/contracts/AccessProtected.sol::11 => abstract contract AccessProtected is Context {
```
