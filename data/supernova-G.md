 # G-01 It costs more gas to initialize a variable to 0 , than to let the default be applied 
 ##### Instances of the issue 
 
 https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148

# G-02 Using ++ i cost less gas than using  i++ 
##### Instances 
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353

# G-03 Use unchecked in case of For loops 
##### Instance
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L35


In case of for loops , as values are within maximum limits, using unchecked reduces gas consumption by 30-40 gas per loop .




