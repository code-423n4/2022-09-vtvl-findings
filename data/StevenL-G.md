First Gas optimization - Using > 0 cost more gas than != when used on a uint in a require() statement.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol
107: require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
256: require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
257: require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
263: require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
272: _cliffReleaseTimestamp > 0 && 
273: _cliffAmount > 0 && 
449: require(bal > 0, "INSUFFICIENT_BALANCE");

Second Gas optimization - Using unchecked blocks can save gas.

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol
381: usrClaim.amountWithdrawn += amountRemaining;
383: numTokensReservedForVesting -= amountRemaining;
429: uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
433: numTokensReservedForVesting -= amountRemaining;