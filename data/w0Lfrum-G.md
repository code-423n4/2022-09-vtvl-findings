# Gas Optimization Report

This report describes ways to optimize the gas costs incurred.

## Lines of Code and Details:

### 1.  VTVLVesting.sol(L353)

[Line 353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration).

It is also better to use unchecked block in order to increment the loop variable. This reduces the gas consumption further.

Consider adding the following code snippet to the VTVLVesting contract:

    function _uncheckedIncrement(uint256 num) internal pure{
        unchecked {
            ++num;   // Pre-increment to save gas
        }
    }

And then use the _uncheckedIncrement function to increment the variable **i** :

        for (uint256 i = 0; i < length; _uncheckedIncrement(i)) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }

Using the unchecked block helps in saving gas for each iteration of the for loop. The default "checked" behavior in solidity version **0.8** costs more gas when incrementing the loop variable. There is no requirement of having the "checked" behavior for the loop variable as the number of recipients will not exceed (2^256) - 1  , which is the max value a uint256 variable can hold. Hence there will not be any overflow scenarios.

### 2.  Functions which are guaranteed to revert when called by normal users can be marked payable( [L325](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L325), [L341](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L341) )

If a function modifier such as **onlyAdmin** is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate admin callers because the compiler will not include checks for whether a payment was provided or not.
