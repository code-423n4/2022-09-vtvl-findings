# Overview
Generally speaking, there are a lot of comments, which is good, but the quality of comments needs to be improved. I have tried to highlight as many technical comment errors as I could, but there are some other general issues.
1) The language also needs a few proofreads. There are many sentences like "[// Let the withdrawal known to everyone](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L390)" which could be rewritten to be clearer. Their cumulative effect is to make the code harder to understand. As this project is designed for general use, these rewrites would be especially worthwhile.
2) There are too many comments which imply that the code is unfinished, or design decisions are still unresolved. This implies to a potential user that the code is not finalised and therefore not stable. All design questions should be fully resolved before release (and before audit) and the comments should reflect that.
___
# Non Critical

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L22
Use the actual variable name in this comment, as on line 23.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L30
Write "function", not "fn" for clarity.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L16
The constructor is missing its comment header.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L29
`isAdmin()` is missing its comment header.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L17
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L83
Note that the variable `tokenAddress` is actually the token, not its address. The address is address(tokenAddress). Consider renaming this to `token`.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L121
Whilst logically this is correct, it could be better explained. The modifier only `require`s one thing, so this could be better expressed.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L159
Remove this comment, or add significant explanation as to why you are mentioning this here (probably better to remove). Also, `cliffReleaseTimestamp` is not allowed to be after `startTimestamp`, so it is doubly confusing.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L258-L260
For clarity and confidence, rewrite these comments as a statement or two, not as a discussion with yourself.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L245
As `_createClaimUnchecked()` consists mostly of checks, consider some alternative names to reflect the fact that it is an internal function without access modifiers: `_permissionlessCreateClaim`, `_privateCreateClaim` or just `_createClaim`

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L365
Remove " - if any" from this comment as `hasActiveClaim(_msgSender())` on line 364 guarantees there will be a claim.


https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398
For clarity, consider renaming `withdrawAdmin` to `adminWithdraw()`. Generally, if the noun is first it is the thing acting. If it is second, it is the thing being acted upon. 

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L203
As in the summary, this is not the sort of comment that should reach production. It will hurt confidence in the code. If the function should be removed, it should already be gone. If not, this comment certainly should be.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266
If you are asking for an opinion, I think it would be good to enforce `_cliffReleaseTimestamp == _startTimestamp == _endTimestamp` in that scenario. Regardless, this issue should be decided and this comment should be removed for confidence and clarity.
___
# Low 
*Technically incorrect comments have been included in this section. Unclear comments are above.*

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L22
The comment refers to an "owner" but there is no such thing in this contract.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L25-L27
To answer the question in the comment: yes, I think you should allow both to be zero. Your reasons for allowing `initialSupply_ == 0` are not affected by your reasons for allowing `maxSupply_ == 0`, or vice versa. I suggest you remove lines 25-27.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39-L43
Note that it is possible with `setAdmin()` to remove all admins and leave the contract without admins permanently. This might be desirable for some contracts, but consider whether it is wanted for this project. If not, one possible protection is to keep a counter of the number of admins and refuse to remove an admin if it would cause the counter to go below 1. The counter's limit could also be an immutable variable set in the constructor to allow extra flexibility.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L126
This comment incorrectly states that an existing claim can have its start timestamp modified in `createClaim()`. Remove the part in brackets to correct it.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L41
This comment is incorrect and contradicts the previous line. It should be removed.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L44
" - released at the cliffReleaseTimestamp" at the end of this comment is incorrect and appears to be accidentally copied from the previous line.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L120
This comment is technically incorrect. They are not opposites as both modifiers will revert if `_claim.startTimestamp == 0` and `_claim.isActive == false`.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L370
Change the comment to "40 bit".

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L245-L304
There is no check that `_endTimestamp` is not in the past, even `_linearVestAmount > 0`.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L53
The value of the state variable `vestingRecipients` seems a little dubious. It will contain all recipients ever, even those with revoked claims or claims that reached their end time long ago. Consider implementing an enumerable mapping library to have a list which contains only the addresses with active claims.
