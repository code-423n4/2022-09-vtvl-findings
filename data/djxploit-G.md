## Storage variables should be cached

Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimisations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 3 instances of this issue:*

```
File: VTVLVesting.sol

374:        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
377:        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
// usrClaim.amountWithdrawn can be cached here , to save 1 SLOAD

426:        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
429:        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
//  _claim.amountWithdrawn can be cached to save 1 SLOAD

File: VariableSupplyERC20Token.sol

40:        if(mintableSupply > 0) {
41:            require(amount <= mintableSupply, "INVALID_AMOUNT");
43:            mintableSupply -= amount;
// mintableSupply can be cached to save 2 SLOADS

```

## `Storage` variables/structs can be changed to `memory` to save gas.

In the below instances, all structs can be changed to memory instead of storage. Because there is no state change happening, only
state load is happening. So to read value, we can use memory to save gas. As SLOAD costs more than MLOAD

*There are 5 instances of this issue:*

```
File: VTVLVesting.sol

106:        Claim storage _claim = claims[_recipient];
124:        Claim storage _claim = claims[_recipient];
197:        Claim storage _claim = claims[_recipient];
207:        Claim storage _claim = claims[_recipient];
216:        Claim storage _claim = claims[_recipient];

```

## Emitting storage variable is costly. So avoid it

*There are 1 instances of  this issue:*

```
File: VTVLVesting.sol

436:        emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);
```

## Using bools for storage incurs overhead

```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

*There are 1 instances of this issue:*

```
File: VTVLVesting.sol

45:        bool isActive; 
```

## It costs more gas to initialise variables to zero than to let the default of zero be applied

*There are 3 instances of this issue:*

```
File: VTVLVesting.sol

27:         uint112 public numTokensReservedForVesting = 0;
148:        uint112 vestAmt = 0;
353:       for (uint256 i = 0; i < length; i++) {
```

## `++i` costs less gas than `i++`, especially when it's used in for-loops

Saves 6 gas per loop.

*There are 1 instances of this issue:*

```
File: VTVLVesting.sol

353:       for (uint256 i = 0; i < length; i++) {
```

## Usage of `uints/ints` smaller than 32 bytes (256 bits) incurs overhead

```
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
```
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Use a larger size then downcast where needed

*There are 8 instances of this issue:*

```
File: VTVLVesting.sol

27:        uint112 public numTokensReservedForVesting = 0;
35:        uint40 startTimestamp; // When does the vesting start (40 bits is enough for TS)
36:        uint40 endTimestamp; // When does the vesting end - the vesting goes linearly between the start and end timestamps
37:        uint40 cliffReleaseTimestamp; // At which timestamp is the cliffAmount released. This must be <= startTimestamp
38:        uint40 releaseIntervalSecs; // Every how many seconds does the vested amount increase. 
42:        uint112 linearVestAmount; // total entitlement
43:        uint112 cliffAmount; // how much is released at the cliff
44:        uint112 amountWithdrawn; // how much was withdrawn thus far - released at the cliffReleaseTimestamp
```

## Usage of `>=` or `<=` is cheaper than `>` or `<`

*There are 3 instances of this issue:*

```
File: VTVLVesting.sol

374:        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
262:        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
426:        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
```

## Use !=0 instead of > 0 inside require statements to save gas

*There are 9 instances of this issue:*

```
File: VTVLVesting.sol

107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); 
257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
272:                _cliffReleaseTimestamp > 0 && 
273:                _cliffAmount > 0 && 
449:        require(bal > 0, "INSUFFICIENT_BALANCE");

File: VariableSupplyERC20Token.sol

27:        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

File: FullPremintERC20Token.sol

11:        require(supply_ > 0, "NO_ZERO_MINT");
```

## Save gas by efficient variable packing

In the below issue , the variable packing was taking 3 slots (uint40+uint40+uint40+uint40+     uint112+uint112+     uint112+bool). But 
if we arrange it differently, it takes 2 slot (uint40+uint40+uint40+uin112+bool   uint40+uint112+uin112). Hence we can save 1 slot here

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L32-L46

## Split multiple conditions inside a require statements to various require statements containing a single condition, to save gas

*There are 2 instances:*
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270-L278
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L351

## Avoid emitting timestamp, as it is implicitly sent in every events

*There are 1 instances of this issue:*
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L436
