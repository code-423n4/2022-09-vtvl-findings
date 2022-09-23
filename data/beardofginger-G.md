# > 0 is less efficient than != 0 for unsigned integers
The gas cost can be reduced by using != 0 instead of > 0. There are 11 occurrences accross 3 contracts:

### VTVLVesting.sol

Line 107:

		require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

Line 256 and 257:

		require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
		require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

Line 263:

		require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");


Line 272 and 273:

		_cliffReleaseTimestamp > 0 && 
		_cliffAmount > 0 && 

Line 449:	

		require(bal > 0, "INSUFFICIENT_BALANCE");

### token/FullPremintERC20Token.sol

Line 11:

		require(supply_ > 0, "NO_ZERO_MINT");

### token/VariableSupplyERC20Token.sol

Line 27:

		require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

Line 31:	

		if(initialSupply_ > 0) {

Line 40:	

		if(mintableSupply > 0) {


# i++ is less efficient than ++i
The gas cost can be reduced by using ++i or i += 1 instead of i++. This occurs on line 352 in  TVLVesting.sol:

		for (uint256 i = 0; i < length; i++) {

# VariableSupplyERC20Token:mint - mintableSupple should be cached
The stored variable mintableSupply is now read from twice, when only once is necessary using the suggested optimised code below.

Current code:

		function mint(address account, uint256 amount) public onlyAdmin {
			require(account != address(0), "INVALID_ADDRESS");
			// If we're using maxSupply, we need to make sure we respect it
			// mintableSupply = 0 means mint at will
			if(mintableSupply > 0) {
				require(amount <= mintableSupply, "INVALID_AMOUNT");
				// We need to reduce the amount only if we're using the limit, if not just leave it be
				mintableSupply -= amount;
			}
			_mint(account, amount);
		}

Optimised code:

		function mint(address account, uint256 amount) public onlyAdmin {
			require(account != address(0), "INVALID_ADDRESS");
			uint256 _mintableSupply = mintableSupply;
			// If we're using maxSupply, we need to make sure we respect it
			// mintableSupply = 0 means mint at will
			if(_mintableSupply > 0) {
				require(amount <= _mintableSupply, "INVALID_AMOUNT");
				// We need to reduce the amount only if we're using the limit, if not just leave it be
				_mintableSupply -= amount;
			}
			mintableSupply = _mintableSupply;
			_mint(account, amount);
    	}    

# VTVLVesting:_createClaimUnchecked  - numTokensReservedForVesting should be cached
The stored variable numTokensReservedForVesting is now read from twice, when only once is necessary using the suggested optimised code below.

Current code:

		// Still no effects up to this point (and tokenAddress is selected by contract deployer and is immutable), so no reentrancy risk 
		require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

		// Done with checks

		// Effects limited to lines below
		claims[_recipient] = _claim; // store the claim
		numTokensReservedForVesting += allocatedAmount; // track the allocated amount
		vestingRecipients.push(_recipient); // add the vesting recipient to the list
		emit ClaimCreated(_recipient, _claim); // let everyone know

Optimised code:

		// Still no effects up to this point (and tokenAddress is selected by contract deployer and is immutable), so no reentrancy risk 
		uint112 _numTokensReservedForVesting = numTokensReservedForVesting;
		require(tokenAddress.balanceOf(address(this)) >= _numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

		// Done with checks

		// Effects limited to lines below
		claims[_recipient] = _claim; // store the claim
		_numTokensReservedForVesting += allocatedAmount; // track the allocated amount
		numTokensReservedForVesting = _numTokensReservedForVesting;
		vestingRecipients.push(_recipient); // add the vesting recipient to the list
		emit ClaimCreated(_recipient, _claim); // let everyone know 