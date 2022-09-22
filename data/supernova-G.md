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

# G-04 Use the power of events rather than array.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L53

An internal array is used for determining the recipients addresses . This array is not used in any logic in the code . Just for front end purposes I suppose. 
This is a very inefficient way of dealing with this problem. 

Instead use events for capturing new Recipients . This way you can get the full list of claimants and also calculate the total No. of Claimants in the front end.

Doing this will remove the expensive address array and remove the 2 getter functions,namely : numVestingRecipients() and allVestingRecipients()

