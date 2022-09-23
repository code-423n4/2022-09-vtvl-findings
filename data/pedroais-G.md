Use of unchecked math at multiple instances can save a lot of gas. This doesn't propose any risks since variables are already being checked.

AVG gas before unchecked arithmetic  = 3740739
AVG gas after = 3677691

Instances :

1- https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L301
This addition can't overflow since allocated amount is capped by token balance in line 295


2- https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L161
Same reason as before, capped by token balance

3- https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L381
Both operations, same reason


4- https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L166

This block could use unchecked arithmetic since :

we know _referenceTs > _claim.startTimestamp so substraction won't underflow 

currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs : can't overflow since it's a truncation 

_claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs : can't overflow since  linear vestAmount is capped by the balance of tokens and truncatedCurrentVestingDuration is a truncation of refereceTS-startTimestamp with referenceTS = to block.timestamp in a valid call. 


5- 
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L429
we know final best amount is bigger so no underflow

6-
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L433
amount remaining cant be bigger than total allocation



++i cheaper than i++ 
Instances :
1- https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353



