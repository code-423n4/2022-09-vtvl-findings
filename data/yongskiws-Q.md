# Dangerous usage of `block.timestamp` (timestamp)

❌• _referenceTs > _claim.endTimestamp (contracts/VTVLVesting.sol#154)
     • _referenceTs >= _claim.cliffReleaseTimestamp (contracts/VTVLVesting.sol#160)
     • _referenceTs > _claim.startTimestamp (contracts/VTVLVesting.sol#166)
     • (vestAmt > _claim.amountWithdrawn) (contracts/VTVLVesting.sol#187)

❌• require(bool,string)(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount,INSUFFICIENT_BALANCE) (contracts/VTVLVesting.sol#295)

❌• require(bool,string)(_claim.amountWithdrawn < finalVestAmt,NO_UNVESTED_AMOUNT) (contracts/VTVLVesting.sol#426)

❌• require(bool,string)(allowance > usrClaim.amountWithdrawn,NOTHING_TO_WITHDRAW) (contracts/VTVLVesting.sol#374)

❌• require(bool,string)(amountRemaining >= _amountRequested,INSUFFICIENT_BALANCE) (contracts/VTVLVesting.sol#402)

# Reentrancy vulnerabilities leading to out-of-order Events

❌   • tokenAddress.safeTransfer(_msgSender(),amountRemaining) (contracts/VTVLVesting.sol#388)
	• Claimed(_msgSender(),amountRemaining) (contracts/VTVLVesting.sol#391)

❌ Reentrancy in VTVLVesting.withdrawAdmin(uint112) (contracts/VTVLVesting.sol:398-411):
	• tokenAddress.safeTransfer(_msgSender(),_amountRequested) (contracts/VTVLVesting.sol#407)
	• AdminWithdrawn(_msgSender(),_amountRequested) (contracts/VTVLVesting.sol#410)

# Multiple calls in a loop (calls-loop)

❌ VTVLVesting._createClaimUnchecked(address,uint40,uint40,uint40,uint40,uint112,uint112) (contracts/VTVLVesting.sol:245-304) has external calls inside a loop: require(bool,string)(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount,INSUFFICIENT_BALANCE) (contracts/VTVLVesting.sol#295)



