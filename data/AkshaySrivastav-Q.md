
1. The `setAdmin()` function in AccessProtected.sol can be used to revoke all admins. This could be a feature to completely renounce ownership of the contract after all claims are set or could be a bug in which one admin either intentionally or unintentionally removes all admin (or all other admins except himself).

2. Line 161 in `VTVLVesting._baseVestedAmount()` function should not get executed when `cliffAmount` is 0. In the case of no cliff amount, i.e. where `cliffReleaseTimestamp` and `cliffAmount` are both set as 0, the program execution should not enter the `if` block.
    ```
    160    if(_referenceTs >= _claim.cliffReleaseTimestamp) {
    161        vestAmt += _claim.cliffAmount;
    162    }
    ```
3. Solidity pragma versioning should be upgraded to latest available version. Currently the solidity version in contracts is ^0.8.14 which was found to possess some bugs.

4. Solidity pragma versioning should be exactly same in all contracts. Currently some contracts use ^0.8.14 but some are fixed to 0.8.14.

5. No need to re-inherit Context contract in [VTVLVesting](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L11) smart contract as Context is already inherited by [AccessProtected](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L11) contract.

6. Ownable smart contract is unnecessarily imported in [AccessProtected.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L5) while it is never used. Unnecessary imports decreases the readability of smart contract code. 

7. Unnecessary imports are also present in [VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L5-L7). The compilation works completely fine with just importing SafeERC20.sol and AccessProtected.sol.

8. AccessProtected - contract docs do not match implementaion. The implementation only has multiple equal rights admins and no `owner` field is present while the docs states something else.
    ```javascript
    7    /** 
    8        @title Access Limiter to multiple owner-specified accounts.
    9        @dev Exposes the onlyAdmin modifier, which will revert (ADMIN_ACCESS_REQUIRED) if the caller is not the owner nor the admin.
    10    */
    ```
9. `VariableSupplyERC20Token.constructor()` has an empty `@dev` tag.

10. VariableSupplyERC20Token contract mentions an incorrect comment
    ```javascript
    48    // We can't really have burn, because that could make our vesting contract not work.
    49    // Example: if the user can burn tokens already assigned to vesting schedules, it could be unable to pay its obligations.

    ```
    Token can be made burnable in which users can be allowed to burn their own tokens.

11. Line 159 in `VTVLVesting._baseVestedAmount()` contains a misleading comment
    ```javascript
    159        // We don't check here that cliffReleaseTimestamp is after the startTimestamp 
    160        if(_referenceTs >= _claim.cliffReleaseTimestamp) {
    161            vestAmt += _claim.cliffAmount;
    162        }

    ``` 
    `cliffReleaseTimestamp` can never be after `startTimestamp` as per the [`require`](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L274) statements of `_createClaimUnchecked()`.

12. As per the implementation of vesting contract, Line 21 in VTVLVesting.sol should mention _greater than or equal_ instead of just _greater than_.  
    ```javascript
    21    /// @dev Our balance of the token must always be greater than this amount.
    ```
13. `VTVLVesting.ClaimCreated` and `VTVLVesting.ClaimRevoked` events should also log the admin's address so it can be easily queried which admin created and revoked the claim.

14. In VTVLVesting contract, before revoking a claim the contract should transfer all the pending/partially vested rewards. Otherwise the entire vesting amount will get revoked. 

    It is at the discretion of protocol development team to decide whether the current behaviour is intended or not.

15. At [Line 82](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L82) of `VTVLVesting.constructor()`, a better check would be to do `_tokenAddress.totalSupply()`. As this will also ensure that the input address in indeed a token's address and perform the zero address check as well.
    ```
    82    require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
    ```

16. The `tokenAddress` state variable of VTVLVesting should be renamed to `token` as this variable represents an `IERC20` interface rather that just an address. Renaming it to `token` aligns better with its usage.
    ```
    17    IERC20 public immutable tokenAddress;
    ```

17. There should be a factory contract for VTVLVesting contract which can keep track of all vesting contracts deployed by different founders. The Factory contract aligns better with the business usecase of VTVL protocol owners.

    From the spec, "The core function of VTVL is to allow users to generate and deploy token vesting smart contracts through our platform."

18. In `VariableSupplyERC20Token.mint()` function, non-zero input validation check should be done similar to `FullPremintERC20Token.constructor()`.

19. In all solidity files, license keyword should be mentioned as `// SPDX-License-Identifier: UNLICENSED`.

20. All the actors interacting with a VTVLVesting contract need to fully trust all of its admins. Any one of the potentially infinite admins of VTVLVesting contract has the power to (either intentionally or unintentionally):
    - revoke claims of all recipients and withdraw all tokens, resulting in a rugpull attack.
    - give or take back the admin rights to or from any ethereum address.
    - withdraw any other ERC20 token from the vesting contract. 

