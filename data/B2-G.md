## ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops
The `unchecked` keyword is new in solidity version 0.8.0, so  this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP
    
    File:  contracts/VTVLVesting.sol   #1

    for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }

*  https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353-L355

## Using > 0 costs more gas than != 0 when used on a uint in a require() statement
This change saves 6 gas per instance

    File: contracts/token/FullPremintERC20Token.sol   #1

    require(supply_ > 0, "NO_ZERO_MINT");

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11

      File: contracts/token/VariableSupplyERC20Token.sol   #2

      require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27

      File: contracts/VTVLVesting.sol   #3

      require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107

       File: contracts/VTVLVesting.sol   #4

       require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256

      File: contracts/VTVLVesting.sol   #5

      require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257

      File: contracts/VTVLVesting.sol   #6

      require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263

      File: contracts/VTVLVesting.sol   #7

       _cliffReleaseTimestamp > 0 && 
                _cliffAmount > 0 && 

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272-L273

      File: contracts/VTVLVesting.sol   #8

      require(bal > 0, "INSUFFICIENT_BALANCE");

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449

## It costs more gas to initialize variables to zero than to let the default of zero be applied

    File: contracts/VTVLVesting.sol   #1

    uint112 public numTokensReservedForVesting = 0;


* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27

      File: contracts/VTVLVesting.sol   #2

      uint112 vestAmt = 0;

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148

      File: contracts/VTVLVesting.sol   #3

      for (uint256 i = 0; i < length; i++) {

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

## ++i costs less gas than ++i, especially when it's used in for-loops (--i/i-- too)
Saves 6 gas PER LOOP

    File: contracts/VTVLVesting.sol   #1

    for (uint256 i = 0; i < length; i++) {

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

##  Splitting require() statements that use && saves gas
Instead of using the `&&` operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement (saving 3 gas per &):

    File: contracts/VTVLVesting.sol   #1

    require( 
            (
                _cliffReleaseTimestamp > 0 && 
                _cliffAmount > 0 && 
                _cliffReleaseTimestamp <= _startTimestamp
            ) || (
                _cliffReleaseTimestamp == 0 && 
                _cliffAmount == 0
        ), "INVALID_CLIFF");

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270-L278

      File: File: contracts/VTVLVesting.sol   #2

       require(_startTimestamps.length == length &&
                _endTimestamps.length == length &&
                _cliffReleaseTimestamps.length == length &&
                _releaseIntervalsSecs.length == length &&
                _linearVestAmounts.length == length &&
                _cliffAmounts.length == length, 
                "ARRAY_LENGTH_MISMATCH"
        );

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L351

## Use custom errors rather than revert()/require() strings to save deployment gas
Custom errors are available from solidity version 0.8.4. The instances below match or exceed that version

    File: contracts/token/FullPremintERC20Token.sol   #1

    require(supply_ > 0, "NO_ZERO_MINT");

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11

      File: contracts/token/VariableSupplyERC20Token.sol   (various lines)   #2

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L37
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L41

       File: contracts/AccessProtected.sol   (various lines)   #3

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L25
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L40

       File: contracts/VTVLVesting.sol   (various lines)   #4

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L82
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L129
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L255
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L262
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L264
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L278
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L350
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L402
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L447
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449

## Don’t compare boolean expressions to boolean literals
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

    File: contracts/VTVLVesting.sol   #1

    require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111

       File: contracts/VTVLVesting.sol   #3

       _claim.isActive = false;  

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L432

## <x> += <y> costs more gas than <x> = <x> + <y> for state variables

    File: contracts/VTVLVesting.sol   #1

    numTokensReservedForVesting += allocatedAmount; 

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301

      File: contracts/VTVLVesting.sol   #2

      numTokensReservedForVesting -= amountRemaining;

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L383

      File: contracts/VTVLVesting.sol   #3

      numTokensReservedForVesting -= amountRemaining;

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L433

      File: contracts/token/VariableSupplyERC20Token.sol   #4

      mintableSupply -= amount;

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43

## Optimize if statament

    File: contracts/token/VariableSupplyERC20Token.sol #1

* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40
* https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31
