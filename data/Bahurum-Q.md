# Low severity findings

## 1. `claimableAmount` does not return 0 after a claim has been revoked
After a claim has been revoked, `claimableAmount` ([VTVLVesting.sol#L215-218](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L215-218)) continues to return the amount that would have been claimable if the claim was still active, while in fact the claimable amount of a revoked claim is 0.
Check for active state of the claim and return 0 if inactive:

```diff
    function claimableAmount(address _recipient) external view returns (uint112) {
        Claim storage _claim = claims[_recipient];
-       return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
+       return _claim.isActive ? (_baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn) : 0;
    }
```

## 2. Missing check on admin disabling its admin role
In function `setAdmin()` ([AccessProtected.sol#L39](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39)) there is no check to prevent an admin disabling itself. This can lead to loss of funds in the following scenario:  
1. There is only one admin of `VTVLVesting` contract
2. admin funds tokens to `VTVLVesting` and wants to delegate the claim creation to another account, so it doesn't create the claims yet.
3. The admin goes on to nominate another admin, but by error calls `setAdmin(admin, false)`, where `admin` is its own address
4. admin is not an admin anymore and no one can recover the tokens sent to `VTVLVesting`.

## 3. Implementation is custodian despite website stating the contrary
The [website](https://www.vtvl.io/) states:  
_VTVL is a non-custodian service. Any tokens which are minted or vesting will remain in a founder’s wallet and directly transferred to the third party wallet appointed in the smart contract._  
This is not true since a 'founder' has to deposit tokens into `VTVLVesting`  before being able to create a claim. So the contract is cusodian of the founder's funds until they are withdrawn by the designated recipient.  
This could cause issues with the risk assumptions made by users and user expectations when they use the product for the first time and in fact they see that the tokens must be sent to the contract.
Update the website accordingly.

# Non critical findings

## 1. Missing or incomplete Natspec comments 
Consider putting Natspec comments on all public/external functions. Occurrencies are:
-   missing natspec comments: [FullPremintERC20Token.sol#L10](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L10), [VariableSupplyERC20Token.sol#L36](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L36), [AccessProtected.sol#L29](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L29)
-   Incomplete naspec comment (missing `param` and `return`): [VTVLVesting.sol#L331](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L331)

## 2. Redundant or unused code
-   In [AccessProtected.sol#L5](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L5) and [VTVLVesting.sol#L6](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L6) `Ownable.sol` is imported but not used.
-   In [VTVLVesting.sol#L111](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111) there is boolean equality. Consider just using `require(_claim.isActive, "NO_ACTIVE_CLAIM")`.

## 3. Public functions could be external
Functions `setAdmin()` ([AccessProtected.sol#L39](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39)) and `withdrawAdmin()` [VTVLVesting.sol#L398](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398) are public but are never called in their own contract. COnsider making these functions external.

## 4. confusion between ERC20 and address type in variable names
The variable `tokenAddress` declared at [VTVLVesting.sol#L17](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L17) is of type `IERC20` and not `address`. Consider renaming it to `token`. Same is valid for variable `_otherTokenAddress` at [VTVLVesting.sol#L446](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L446), consider renaming it to `_otherToken`.

## 5. Burnable tokens don't cause issues with the vesting contract but comments state the contrary
In [VariableSupplyERC20Token.sol#L48-L49](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L48-L49) there is a comment stating that burnging is not implemented since this would create issues with the vesting contract. As long as a normal `burn` function that allows a token owner to burn his own tokens is added, there will be no issues with the vesting contract. The vesting contract holds the tokens and no one else can burn them after they are transfered to it.

## 6. Inaccurate comment on `uint40` size
In [VTVLVesting.sol#L370](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L370) the comment states that the `uint40` type has 48 bits. It has 40 bits.
