## Inexpedient Commented Code
It is not expedient commenting directly on unusable codes. Consider rewriting/removing them:

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L114-L115
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L133
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L136-L138   
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L261

## Two-step New Admin(s) Enabled
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39-L43

`setAdmin()` should be implemented giving admin role to another address in a pending state. And then, a `claimAdmin()` should be introduced for the new admin(s) to claim admin role. This will ensure the new set of admins are going to be fully aware of their new roles granted.

## Typo Mistake
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L168

@ calculated
// Next, we need to calculated the duration truncated to nearest releaseIntervalSecs