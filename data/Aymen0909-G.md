# Gas Optimizations

## Findings Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Usage of `uints/ints` smaller than 32 bytes (256 bits) incurs overhead  |  1 |
| 2      | Use `unchecked` blocks to save gas  |  5 |
| 3      | Cache `storage` values in `memory` to minimize SLOADs  |  2 |
| 4      | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  5 |
| 5      | Use custom errors rather than `require()`/`revert()` strings to save deployments gas  |  24 |
| 6      | Splitting `require()` statements that uses `&&` saves gas  |  2 |
| 7      | It costs more gas to initialize variables to zero than to let the default of zero be applied    |  3  |
| 8      | Use of `++i` cost less gas than `i++` in for loops    |  1  |
| 9      | `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  1  |
| 10     | Functions guaranteed to revert when called by normal users can be marked payable |  6 |

## Savings Summary

* Total gas saved : 3168 gas

| function      | Before        |    After     | difference   |
| ------------- |:-------------:|:------------:|:------------:|
| createClaim      | 167759     | 167485     |    -274     |
| createClaimsBatch      | 284486     | 283710    |    -776     |
| revokeClaim      | 40819     | 39881     |    -938     |
| setAdmin      | 37467     | 37443      |    -24     |
| withdraw      | 72938     | 71872     |    -1066     |
| withdrawAdmin      | 54058     | 53992     |    -66     |
| withdrawOtherToken     | 56577    | 56553   |    -24     |
|      |      |    |   -3168     |

## Findings

### 1- Usage of `uints/ints` smaller than 32 bytes (256 bits) incurs overhead (saves ~1127 gas):

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size as you can check [here](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html).

So use `uint256`/`int256` for state variables and then downcast to lower sizes where needed.

There is 1 instance of this issue:

File: contracts/VTVLVesting.sol [Line 27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27)
```
uint112 public numTokensReservedForVesting = 0;
```

* Gas savings : in total 1127 gas saved

| function      | Before        |    After     | difference   |
| ------------- |:-------------:|:------------:|:------------:|
| createClaim      | 167759     | 167522     |    -237     |
| createClaimsBatch      | 284486     | 284010    |    -476     |
| revokeClaim      | 40819     | 40637     |    -182     |
| withdraw      | 72938     |72748     |    -190     |
| withdrawAdmin      | 54058     |54016     |    -42     |
|      |      |    |   -1127     |

### 2- Use `unchecked` blocks to save gas (saves ~1164 gas) :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 5 instances of this issue:

File: contracts/VTVLVesting.sol [Line 167](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L167)

```
uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp;
```

The above operation cannot underflow due to the check [_referenceTs > _claim.startTimestamp](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L166) and should be modified as follows :

```
uint40 currentVestingDurationSecs;
unchecked {
    currentVestingDurationSecs = _referenceTs - _claim.startTimestamp;
}
```

File: contracts/VTVLVesting.sol [Line 170](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L170)

```
uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp;
```

The above operation cannot underflow due to the check [require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP");](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L262) and should be modified as follows :

```
uint40 finalVestingDurationSecs;
unchecked {
    finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp;
}
```

File: contracts/VTVLVesting.sol [Line 377](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L377)

```
uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
```

The above operation cannot underflow due to the check [require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L374) and should be modified as follows :

```
uint112 amountRemaining;
unchecked {
    amountRemaining = allowance - usrClaim.amountWithdrawn;
}
```

File: contracts/VTVLVesting.sol [Line 429](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L429)

```
uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
```

The above operation cannot underflow due to the check [require(_claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426) and should be modified as follows :

```
uint112 amountRemaining;
unchecked {
    amountRemaining = finalVestAmt - _claim.amountWithdrawn;
}
```

File: contracts/token/VariableSupplyERC20Token.sol [Line 43](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43)

```
mintableSupply -= amount;
```

The above operation cannot underflow due to the check [require(amount <= mintableSupply, "INVALID_AMOUNT");]() and should be modified as follows :

```
unchecked {
    mintableSupply -= amount;
}
```

* Gas savings : in total 1164 gas saved

| function      | Before        |    After     | difference   |
| ------------- |:-------------:|:------------:|:------------:|
| revokeClaim      | 40819     | 40237     |    -582     |
| withdraw      | 72938     |72356     |    -582     |
|      |      |    |   -1164     |

### 3- Cache `storage` values in `memory` to minimize SLOADs (saves ~250 gas):

The code can be optimized by minimising the number of SLOADs. SLOADs are expensive 100 gas compared to MLOADs/MSTOREs(3 gas)
Storage value should get cached in memory

There are 2 instances of this issue:

File: contracts/VTVLVesting.sol [function withdraw](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L364-L392)

`withdraw()`: `usrClaim.amountWithdrawn` should be cached (Saves ~ 129 gas) because it's value is read twice meaning 2 SLOAD 

The `withdraw` function shoud be modified as follow :

```
    function withdraw() hasActiveClaim(_msgSender()) external {
        // Get the message sender claim - if any

        Claim storage usrClaim = claims[_msgSender()];

        // we can use block.timestamp directly here as reference TS, as the function itself will make sure to cap it to endTimestamp
        // Conversion of timestamp to uint40 should be safe since 48 bit allows for a lot of years.
        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));

        // @audit : cach usrClaim.amountWithdrawn into memory variable
        uint112 _amountWithdrawn = usrClaim.amountWithdrawn;

        // Make sure we didn't already withdraw more that we're allowed.
        require(allowance > _amountWithdrawn, "NOTHING_TO_WITHDRAW");

        // Calculate how much can we withdraw (equivalent to the above inequality)
        uint112 amountRemaining = allowance - _amountWithdrawn;

        // "Double-entry bookkeeping"
        // Carry out the withdrawal by noting the withdrawn amount, and by transferring the tokens.
        usrClaim.amountWithdrawn += amountRemaining;
        // Reduce the allocated amount since the following transaction pays out so the "debt" gets reduced
        numTokensReservedForVesting -= amountRemaining;
        
        // After the "books" are set, transfer the tokens
        // Reentrancy note - internal vars have been changed by now
        // Also following Checks-effects-interactions pattern
        tokenAddress.safeTransfer(_msgSender(), amountRemaining);

        // Let withdrawal known to everyone.
        emit Claimed(_msgSender(), amountRemaining);
    }
```

File: contracts/VTVLVesting.sol [function revokeClaim](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L418-L437)

`revokeClaim(address _recipient)`: `_claim.amountWithdrawn` should be cached (Saves ~ 121 gas) because it's value is read twice meaning 2 SLOAD 

The `revokeClaim` function shoud be modified as follow :

```
    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {
        // Fetch the claim
        Claim storage _claim = claims[_recipient];
        // Calculate what the claim should finally vest to
        uint112 finalVestAmt = finalVestedAmount(_recipient);
        
        // @audit : cach _claim.amountWithdrawn into memory variable
        uint112 _amountWithdrawn = _claim.amountWithdrawn;

        // No point in revoking something that has been fully consumed
        // so require that there be unconsumed amount
        require( _amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

        // The amount that is "reclaimed" is equal to the total allocation less what was already withdrawn
        uint112 amountRemaining = finalVestAmt - _amountWithdrawn;

        // Deactivate the claim, and release the appropriate amount of tokens
        _claim.isActive = false;    // This effectively reduces the liability by amountRemaining, so we can reduce the liability numTokensReservedForVesting by that much
        numTokensReservedForVesting -= amountRemaining; // Reduces the allocation

        // Tell everyone a claim has been revoked.
        emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);
    }
```

* Gas savings : in total 250 gas saved

| function      | Before        |    After     | difference   |
| ------------- |:-------------:|:------------:|:------------:|
| revokeClaim      | 40819     | 40698     |    -121     |
| withdraw      | 72938     | 72809     |    -129     |
|      |      |    |   -250     |

### 4- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables (saves ~45 gas) :

There are 5 instances of this issue:

File: contracts/VTVLVesting.sol [Line 301](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301)
```
numTokensReservedForVesting += allocatedAmount;
```

File: contracts/VTVLVesting.sol [Line 381](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381)
```
usrClaim.amountWithdrawn += amountRemaining;
```

File: contracts/VTVLVesting.sol [Line 383](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L383)
```
numTokensReservedForVesting -= amountRemaining;
```

File: contracts/VTVLVesting.sol [Line 433](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L433)
```
numTokensReservedForVesting -= amountRemaining;
```

File: contracts/token/VariableSupplyERC20Token.sol [Line 43](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43)
```
mintableSupply -= amount;
```    

* Gas savings : in total 45 gas saved

| function      | Before        |    After     | difference   |
| ------------- |:-------------:|:------------:|:------------:|
| createClaim      | 167759     | 167750     |    -9     |
| createClaimsBatch      | 284486     | 284458    |    -26     |
| withdraw      | 72938     | 72928     |    -10     |
|      |      |    |   -45     |

### 5- Use custom errors rather than `require()`/`revert()` strings to save deployments gas :

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information.

There are 24 instances of this issue :

```
File: contracts/VTVLVesting.sol

82       require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
107      require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
111      require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
129      require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
255      require(_recipient != address(0), "INVALID_ADDRESS");
256      require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); 
257      require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
262      require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP");
263      require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL"); 
264      require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
270      require( 
            (
                _cliffReleaseTimestamp > 0 && 
                _cliffAmount > 0 && 
                _cliffReleaseTimestamp <= _startTimestamp
            ) || (
                _cliffReleaseTimestamp == 0 && 
                _cliffAmount == 0
        ), "INVALID_CLIFF");
295      require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
344      require(_startTimestamps.length == length &&
                _endTimestamps.length == length &&
                _cliffReleaseTimestamps.length == length &&
                _releaseIntervalsSecs.length == length &&
                _linearVestAmounts.length == length &&
                _cliffAmounts.length == length, 
                "ARRAY_LENGTH_MISMATCH"
        );
374      require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
402      require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE"); 
429      require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT"); 
447      require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); 
449      require(bal > 0, "INSUFFICIENT_BALANCE");

File: contracts/AccessProtected.sol

25       require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED"); 
40       require(admin != address(0), "INVALID_ADDRESS"); 

File: contracts/token/FullPremintERC20Token.sol

11       require(supply_ > 0, "NO_ZERO_MINT"); 

File: contracts/token/VariableSupplyERC20Token.sol

27       require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT"); 
37       require(account != address(0), "INVALID_ADDRESS");
41       require(amount <= mintableSupply, "INVALID_AMOUNT"); 
```

### 6- Splitting `require()` statements that uses `&&` saves gas (saves 8 gas per &&) :

There are 2 instances of this issue :

```
File: contracts/VTVLVesting.sol

270      require( 
            (
                _cliffReleaseTimestamp > 0 && 
                _cliffAmount > 0 && 
                _cliffReleaseTimestamp <= _startTimestamp
            ) || (
                _cliffReleaseTimestamp == 0 && 
                _cliffAmount == 0
        ), "INVALID_CLIFF");
344      require(_startTimestamps.length == length &&
                _endTimestamps.length == length &&
                _cliffReleaseTimestamps.length == length &&
                _releaseIntervalsSecs.length == length &&
                _linearVestAmounts.length == length &&
                _cliffAmounts.length == length, 
                "ARRAY_LENGTH_MISMATCH"
        ); 
```

### 7- It costs more gas to initialize variables to zero than to let the default of zero be applied (saves ~3 gas per instance) :

If a variable is not set/initialized, it is assumed to have the default value (0 for uint or int, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 3 instances of this issue:

File: contracts/VTVLVesting.sol [Line 27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27)
```
uint112 public numTokensReservedForVesting = 0;
```

File: contracts/VTVLVesting.sol [Line 148](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148)
```
uint112 vestAmt = 0;
```

File: contracts/VTVLVesting.sol [Line 353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)
```
for (uint256 i = 0; i < length; i++)
```

### 8- Use of `++i` cost less gas than `i++/i=i+1` in for loops (saves ~10 gas) :

There is 1 instance of this issue:

File: contracts/VTVLVesting.sol [Line 353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)
```
for (uint256 i = 0; i < length; i++)
```

* Gas savings : in total 1164 gas saved

| function      | Before        |    After     | difference   |
| ------------- |:-------------:|:------------:|:------------:|
| createClaimsBatch      | 284486     | 284476    |    -10     |
|      |      |    |   -10     |

### 9- `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops (saves ~240 gas) :

There is 1 instance of this issue:

File: contracts/VTVLVesting.sol [Line 353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)
```
for (uint256 i = 0; i < length; i++)
```

* Gas savings : in total 240 gas saved

| function      | Before        |    After     | difference   |
| ------------- |:-------------:|:------------:|:------------:|
| createClaimsBatch      | 284486     | 284246    |    -240    |
|      |      |    |   -240     |


### 10- Functions guaranteed to revert when called by normal users can be marked `payable` (saves ~393 gas) :

If a function modifier such as `onlyAdmin` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 6 instances of this issue:

```
File: contracts/VTVLVesting.sol

317     function createClaim(
            address _recipient, 
            uint40 _startTimestamp, 
            uint40 _endTimestamp, 
            uint40 _cliffReleaseTimestamp, 
            uint40 _releaseIntervalSecs, 
            uint112 _linearVestAmount, 
            uint112 _cliffAmount
                ) external onlyAdmin
333     function createClaimsBatch(
            address[] memory _recipients, 
            uint40[] memory _startTimestamps, 
            uint40[] memory _endTimestamps, 
            uint40[] memory _cliffReleaseTimestamps, 
            uint40[] memory _releaseIntervalsSecs, 
            uint112[] memory _linearVestAmounts, 
            uint112[] memory _cliffAmounts) 
            external onlyAdmin
398     function withdrawAdmin(uint112 _amountRequested) public onlyAdmin
418     function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient)
446     function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin

File: contracts/AccessProtected.sol

39     function setAdmin(address admin, bool isEnabled) public onlyAdmin
```

* Gas savings : in total 393 gas saved

| function      | Before        |    After     | difference   |
| ------------- |:-------------:|:------------:|:------------:|
| createClaim      | 167759     | 167736     |    -23     |
| createClaimsBatch      | 284486     | 284462    |    -24     |
| revokeClaim      | 40819     | 40674     |    -145     |
| setAdmin      | 37467     | 37443     |    -24     |
| withdraw      | 72938     | 72809     |    -129     |
| withdrawAdmin      | 54058     |54034     |    -24     |
| withdrawOtherToken     | 56577    | 56553    |    -24     |
|      |      |    |   -393     |