File: VTVLVesting.sol

G-01. Unchecking arithmetics operations that can’t underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block: https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic

This code could be changed

```
File: VTVLVesting.sol:353

for (uint256 i = 0; i < length; i++) {
_createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
}

```

To this

```
for (uint256 i = 0; i < length;) {
    _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
    unchecked{i++;}
}
```

G-02. ++i costs less gas compared to i++ or i += 1 (same for --i vs i-- or i -= 1)

Pre-increments and pre-decrements are cheaper.

For a uint256 i variable, the following is true with the Optimizer enabled at 10k:

Increment:

i += 1 is the most expensive form
i++ costs 6 gas less than i += 1
++i costs 5 gas less than i++ (11 gas less than i += 1)
Decrement:

i -= 1 is the most expensive form
i-- costs 11 gas less than i -= 1
--i costs 5 gas less than i-- (16 gas less than i -= 1)

Affected code:

```
File: VTVLVesting.sol:353

for (uint256 i = 0; i < length;) {
    _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
    unchecked{++i;}
}
```

G-03. It costs more gas to initialize variables with their default value than letting the default value be applied]

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Affected code:

```
File VTVLVesting.sol

uint112 public numTokensReservedForVesting = 0; -> uint112 public numTokensReservedForVesting;
uint112 vestAmt = 0; -> uint112 vestAmt;


for (uint256 i; i < length;) {
    _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
    unchecked{++i;}
}
```

G-04. Use calldata instead of memory

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memoryindex. Each iteration of this for-loop costs at least 60 gas (i.e. 60 \* <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

When arguments are read-only on external functions, the data location should be calldata:

```
File: VTVLVesting.sol:333

function createClaimsBatch(
    address[] memory _recipients,
    uint40[] memory _startTimestamps,
    uint40[] memory _endTimestamps,
    uint40[] memory _cliffReleaseTimestamps,
    uint40[] memory _releaseIntervalsSecs,
    uint112[] memory _linearVestAmounts,
    uint112[] memory _cliffAmounts)
    external onlyAdmin {
```

low gas

```
function createClaimsBatch(
    address[] calldata _recipients,
    uint40[] calldata _startTimestamps,
    uint40[] calldata _endTimestamps,
    uint40[] calldata _cliffReleaseTimestamps,
    uint40[] calldata _releaseIntervalsSecs,
    uint112[] calldata _linearVestAmounts,
    uint112[] calldata _cliffAmounts)
    external onlyAdmin {
```

G-05. <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

Issues:

```
File: VTVLVesting.sol

vestAmt += linearVestAmount;
numTokensReservedForVesting += allocatedAmount; // track the allocated amount


```

G-06. Use custom errors.

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.


```
Use

error InvalidAddress();

Instead of

require(admin != address(0), "INVALID_ADDRESS");
require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
require(_recipient != address(0), "INVALID_ADDRESS");


```


G-06. Using <X> != 0 instead of <X> > 0 for uint type.

As uint is always a non-zero value, you can save a bit of gas by using != 0 instead of > 0.

```
File: VTVLVesting.sol

require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
```