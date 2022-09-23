Cheaper For Loops - 25 to 80 gas per instance - 9 instances
You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

 for (uint256 i = 0; i < length; /** NOTE: Removed i++ **/ ) {
                // Do the thing
                // Unchecked pre-increment is cheapest
                unchecked { ++i; }
        }       

instance:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353-L355