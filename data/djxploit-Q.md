## Custom error messages should be used instead of revert strings, to save deployment cost

Custom errors from solidity compiler 0.8.4, leads to cheaper deploy time cost and run time cost. Note: the run time cost is only relevant when the revert condition is met. In short, replace revert strings by custom errors.

*There are 16 instances of this issue:*

```
File: VTVLVesting.sol

82:        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
107:       require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
111:       require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
129:      require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
255:      require(_recipient != address(0), "INVALID_ADDRESS");
256:     require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); 
257:      require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
262:      require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); 
263:      require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
264:      require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
295:      require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
374:      require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
402:     require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
426:      require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
447:      require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN");
449:     require(bal > 0, "INSUFFICIENT_BALANCE");
```

## Dependence on block.timestamp

Use of block attributes like block.timestamp are risky, as they can be manipulated by miners

*There are 3 instances of this issue:*

```
File: VTVLVesting.sol

217:        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
371:        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
436:       emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);

```

## Avoid floating pragma

Use specific compilers in the pragma. Compilers should be locked to a particular version.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2

## `receive` function should be implemented to revert any unintended ether received

The contract should implement a `receive()` function, such that whenever any unintended ether is received, a revert will be triggered
This is done , so that the contract can't receive any ether, even through self-destruct functions. Because if the contract receives any ether,
that ether will be permanently locked in the contract. There would be no way of retrieving the locked ether.

## `allVestingRecipients()` may fail when the size of vestingRecipients would be too high

As seen from line https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L53, `vestingRecipients` is an 
unbounded array. So when the size gets big, the function may DOS 
And there is no `pop` function but only `push` function available, as seen in line https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L302

## Add `non-reentrant` modifier 

All the critical functions like withdraw and claim, should use the `non-reentrant` modifier. Though the `check-effect` pattern is followed, still it
is a best practise to implement the `non-reentrant` modifier in all functions. That way, you can be extra sure of preventing any re-entrancy issues.

## Typo in comments 

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L102.
There is a spelling mistake in `IFF`. It should be `If`

