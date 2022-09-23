1.

Missing spacing in comments.
There is supposed to be a space after "//" in:

Contract: FullPremintERC20Token.sol

	line 1

Contract: VariableSupplyERC20Token.sol

	line 1

Contract: AccessProtected.sol

	line 1

Contract: VTVLVesting.sol

	line 1
	
Recommendation:

	// SPDX-License-Identifier: Unlicense

	across all contracts in line 1
	
2.

I will admit that I am uncertain about this but if you look at the pragma, they do align but there is a caveat.
In contract: VariableSupplyERC20Token.sol it states 0.8.14 and up whereas the rest merely rely on 0.8.14.
Again, I am uncertain whether all pragmas should be 0.8.14 and up, just 0.8.14 or if there is no issue at all but
from my understanding of the contracts it would be fine just to use ^0.8.14 on all contracts to "future-proof".

Recommendation:

	pragma solidity ^0.8.14;
	
	across all contracts in line 2

3.

It is best practice and also unnecessary to initialize variables as they get set to 0 by default.

Contract: VTVLVesting.sol

	line 27
	line 148
	
Recommendation:

	uint112 public numTokensReservedForVesting;
	uint112 vestAmt;

4.

It is best practice to start a new statement/sentence in capital letters, comments, or if it overflows to a new statement as with "-".

Contract: VariableSupplyERC20Token.sol

	line 25
	line 30
	line 49
	
Recommendation:

	// However, if both are 0 we might have a valid scenario as well - User just wants to create a token but doesn't want to mint anything
	// Note: The check whether initial supply is less than or equal than mintableSupply will happen in mint fn.
	// Example: If the user can burn tokens already assigned to vesting schedules, it could be unable to pay its obligations.

Contract: VTVLVesting.sol
	
	line 3
	line 42
	line 43
	line 44
	line 45
	line 89
	line 150
	line 152
	line 170
	line 300
	line 301
	line 302
	line 303
	line 369
	line 386
	line 396
	line 405
	line 444
		
Recommendation:

	// Note: Using solidity 0.8, SafeMath not needed any more
	// Total entitlement
	// How much is released at the cliff
	// How much was withdrawn thus far - Released at the cliffReleaseTimestamp
	// Whether this claim is active (or revoked)
	@param _recipient - The address for which we fetch the claim.
	// The condition to have anything vested is to be active
	// No point of looking past the endTimestamp as nothing should vest afterwards
	// Length of the interval
	// Store the claim
	// Track the allocated amount
	// Add the vesting recipient to the list
	// Let everyone know
	// We can use block.timestamp directly here as reference TS, as the function itself will make sure to cap it to endTimestamp
	// Reentrancy note - Internal vars have been changed by now
	@param _amountRequested - The amount that we want to withdraw
	// Reentrancy note - This operation doesn't touch any of the internal vars, simply transfers
	@param _otherTokenAddress - The token which we want to withdraw
	
5.

Contract: VTVLVesting.sol

Deprecated, now commented code should be removed in:

	line 114 - 115
	
	line 113 comment should also be removed with respect to lines 114 - 115

	line 133
	
	line 131 - 132 comments should also be removed with respect to line 133
	
	line 136 - 138
	
	line 135 comment should also be removed with respect to lines 136 - 138

	line 261
	
	line 258 - 260 comments should also be removed with respect to line 261
	
6.

It is best practice and also unnecessary to initialize variables in for loops as they get set to 0 by default.

Contract: VTVLVesting.sol

	line 353
	
Recommendation:

	for (uint256 i; i < length; i++) {
	
7.

Comments can be wrapped in:

Contract: VariableSupplyERC20Token.sol

	line 22 - 26
	
Recommendation:

/**
 * max supply == 0 means mint at will. 
 * initialSupply_ == 0 means nothing preminted
 * Therefore, we have valid scenarios if either of them is 0
 * However, if both are 0 we might have a valid scenario as well - user just wants to create a token but doesn't want to mint anything
 * Should we allow this?
 */

Contract: VTVLVesting.sol

	line 125 - 128
	
	line 172 - 175
	
	line 183 - 186
	
	line 385 - 387
	
	line 404 - 406
	
Recommendation:

/**
 * Start timestamp != 0 is a sufficient condition for a claim to exist
 * This is because we only ever add claims (or modify startTs) in the createClaim function 
 * Which requires that its input startTimestamp be nonzero
 * So therefore, a zero value for this indicates the claim does not exist.
 */      

/** 
 * Calculate the linear vested amount - fraction_of_interval_completed * linearVestedAmount
 * Since fraction_of_interval_completed is truncatedCurrentVestingDurationSecs / finalVestingDurationSecs, the formula becomes
 * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs * linearVestAmount, so we can rewrite as below to avoid 
 * rounding errors
 */	

/**	
 * Return the bigger of (vestAmt, _claim.amountWithdrawn)
 * Rationale: no matter how we calculate the vestAmt, we can never return that less was vested than was already withdrawn.
 * Case where this could be relevant - If the claim is inactive, vestAmt would be 0, yet if something was already withdrawn 
 * on that claim, we want to return that as vested thus far - as we want the function to be monotonic.
 */
 
/**
 * After the "books" are set, transfer the tokens
 * Reentrancy note - internal vars have been changed by now
 * Also following Checks-effects-interactions pattern
 */

/** 
 * Actually withdraw the tokens
 * Reentrancy note - this operation doesn't touch any of the internal vars, simply transfers
 * Also following Checks-effects-interactions pattern
 */
 
Final Note:

Implementing steps 1 - 4 will also provide more code consistency throughout all contracts.
Whereas step 7 will present cleaner contracts to anyone wishing to read them and better comment consistency.