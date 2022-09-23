1  Using > 0 costs more gas than != 0 when used on a uint in a require() statement.Saves 6 gas

- contracts/token/VariableSupplyERC20Token.sol

27  require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

- contracts/token/FullPremintERC20Token.sol

11    require(supply_ > 0, "NO_ZERO_MINT");

- tvl/contracts/VTVLVesting.sol 

107  require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

256  require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
 
257  require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

263  require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

449  require(bal > 0, "INSUFFICIENT_BALANCE");

2  ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 IN FOR LOOPS (~5 GAS PER ITERATION)
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i.++i returns the actual incremented value with i++ the compiler has to create a temporary variable (when used) for returning 1 instead of 2.

-  contracts/VTVLVesting.sol

353 for (uint256 i = 0; i < length; i++)

3 Splitting require() statements that use && saves gas.Saves 8 gas per &&
Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

-   contracts/VTVLVesting.sol
344 require(_startTimestamps.length == length &&
345            _endTimestamps.length == length &&
346           _cliffReleaseTimestamps.length == length &&
347            _releaseIntervalsSecs.length == length &&
348             _linearVestAmounts.length == length &&
349           _cliffAmounts.length == length, 
350           "ARRAY_LENGTH_MISMATCH"

The above should be modified to
 
    require( _startTimestamps.length == length,
                "ARRAY_LENGTH_MISMATCH");
      require(  _endTimestamps.length == length,
                "ARRAY_LENGTH_MISMATCH";
         require(  _cliffReleaseTimestamps.length == length,
                "ARRAY_LENGTH_MISMATCH");
        require( _releaseIntervalsSecs.length == length,
                "ARRAY_LENGTH_MISMATCH");
         require(  _linearVestAmounts.length == length,
                "ARRAY_LENGTH_MISMATCH");
           require( _cliffAmounts.length == length,
                "ARRAY_LENGTH_MISMATCH");

4   Cache storage values in memory to minimize SLOADs

The code can be optimized by minimising the number of SLOADs. SLOADs are expensive 100 gas compared to MLOADs/MSTOREs(3gas)
Storage value should get cached in memory

- contracts/token/VariableSupplyERC20Token.sol 

36function mint(address account, uint256 amount) public onlyAdmin {
 37     require(account != address(0), "INVALID_ADDRESS");
 38      // If we're using maxSupply, we need to make sure we respect it
39      // mintableSupply = 0 means mint at will
40      if(mintableSupply > 0) {
41          require(amount <= mintableSupply, "INVALID_AMOUNT");
42        // We need to reduce the amount only if we're using the limit, if not just leave it be
 43          mintableSupply -= amount;
 44    }
 45      _mint(account, amount);
 46   }
 Caching mintableSupply as a local variable









