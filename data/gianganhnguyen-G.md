# 1. [G-1]  ++i  cost less gas compare i++ in for loops

File VTVLVesting.sol, line 353:

    for (uint256 i = 0; i < length; i++) {
       ...
    }

Becomes:

    for (uint256 i = 0; i < length; ++i) {
        ...
    }

# 2. [G-2] Using uncheck blocks to save gas - increments in for loop can be uncheck

File Vault.sol, line 443:

    for (uint256 i = 0; i < length; i++) {
       ...
    }

Becomes:

    for (uint256 i = 0; i < length; i++) {
          ...
          unchecked { ++i; }
      }

# 3. [G-3] Initialize variable without assign equal 0  is cheaper than initialize with assign equal 0

File VTVLVesting.sol, line 27:

    uint112 public numTokensReservedForVesting = 0;

Becomes:

    uint112 public numTokensReservedForVesting;

File VTVLVesting.sol, line 148:

    uint112 vestAmt = 0;

Becomes:

    uint112 vestAmt;

File VTVLVesting.sol, line 353:

    for (uint256 i = 0; i < length; i++) {

Becomes:

    for (uint256 i; i < length; i++) {

# 4. [G-4] Using uncheck blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

 File VTVLVesting.sol, line 161:

    vestAmt += _claim.cliffAmount;

Becomes:

    unchecked {
      vestAmt += _claim.cliffAmount;
    }

 File VTVLVesting.sol, line 167-179:

    uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp;
    uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
    uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp;
    uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;
    vestAmt += linearVestAmount;

Becomes:

    uint40 currentVestingDurationSecs;
    uint40 truncatedCurrentVestingDurationSecs;
    uint40 finalVestingDurationSecs;
    uint112 linearVestAmount;
    unchecked {
      currentVestingDurationSecs = _referenceTs - _claim.startTimestamp;
      truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
      finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp;
      linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;
      vestAmt += linearVestAmount;
    }

 File VTVLVesting.sol, line 292:

    uint112 allocatedAmount = _cliffAmount + _linearVestAmount;

Becomes:

    uint112 allocatedAmount;
    unchecked {
      allocatedAmount = _cliffAmount + _linearVestAmount;
    }

 File VTVLVesting.sol, line 301:

    numTokensReservedForVesting += allocatedAmount;

Becomes:

    unchecked {
      numTokensReservedForVesting += allocatedAmount;
    }

 File VTVLVesting.sol, line 377-384:

    uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
    usrClaim.amountWithdrawn += amountRemaining;
    numTokensReservedForVesting -= amountRemaining;

Becomes:

    uint112 amountRemaining;
    unchecked {
       amountRemaining = allowance - usrClaim.amountWithdrawn;
       usrClaim.amountWithdrawn += amountRemaining;
       numTokensReservedForVesting -= amountRemaining;
    }

 File VTVLVesting.sol, line 429:

    uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;

Becomes:

    uint112 amountRemaining;
    unchecked {
      amountRemaining = finalVestAmt - _claim.amountWithdrawn;
    }

 File VTVLVesting.sol, line 433:

    numTokensReservedForVesting -= amountRemaining;

Becomes:

    unchecked {
      numTokensReservedForVesting -= amountRemaining;
    }

# 5. [G-5] Cache read variables in memory to save gas

 File VTVLVesting.sol, line 106:

    Claim storage _claim = claims[_recipient];

Becomes:

    Claim memory _claim = claims[_recipient];

##### Instances include:

 File VTVLVesting.sol, line 124, 197, 207, 216.

# 6. [G-6]  <x> += <y> cost more gas than <x> = <x> + <y>

 File VTVLVesting.sol, line 161:

    vestAmt += _claim.cliffAmount;

Becomes:

    vestAmt = vestAmt + _claim.cliffAmount;

 File VTVLVesting.sol, line 383:

    numTokensReservedForVesting -= amountRemaining;

Becomes:

    numTokensReservedForVesting = numTokensReservedForVesting - amountRemaining;

##### Instances include:

 File VTVLVesting.sol, line 179, 301, 381,  433.
 File VariableSupplyERC20Token.sol, line 43.