# N-01 Public Function which can only be called by admin should be marked as external instead

##### Instance of the issue :

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398
# N-02 Variable Names that are immutable should be in Capital Letters

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L17

# N-03 Missing event for withdrawing other ERC20 addresses
 
It should be a transparent information when the admin withdraws ERC20 tokens from the contract. 
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L446

# N-04 Missing Natspec - Author , Title of the contract 
Author and Title are an important information for contract users and stakeholders. 
Consider adding it 
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L11

# L-01 Open TODOS

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266

# L-02 Consider 2 phase admin addition pattern

To prevent a wrong address being made as an admin , consider making the new admin accept the admin rights before updating his rights.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39

# L-03 vestingRecipients is not updated when a claim is revoked
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L53

The recipient is not deleted from the array when a claim is revoked, thereby showing wrong information in the frontend