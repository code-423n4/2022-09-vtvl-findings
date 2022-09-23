# LOW


## Remove record of `vestingRecipients`

There is no need to keep record of vestingRecipients and keep this logic, you will save gas and remove unnecesary calculations by doing so.
My recomendation is to keep track of `vestingRecipients` and `Claims` using events, there are complete solutions to tackle this issua like https://thegraph.com/en/


```diff
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..7423bca 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -49,9 +49,6 @@ contract VTVLVesting is Context, AccessProtected {
     // Only one Claim possible per address
     mapping (address => Claim) internal claims;
 
-    // Track the recipients of the vesting
-    address[] internal vestingRecipients;
-
     // Events:
     /**
     @notice Emitted when a founder adds a vesting schedule.
@@ -217,20 +214,6 @@ contract VTVLVesting is Context, AccessProtected {
         return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
     }
     
-    /** 
-    @notice Return all the addresses that have vesting schedules attached.
-    */ 
-    function allVestingRecipients() external view returns (address[] memory) {
-        return vestingRecipients;
-    }
-
-    /** 
-    @notice Get the total number of vesting recipients.
-    */
-    function numVestingRecipients() external view returns (uint256) {
-        return vestingRecipients.length;
-    }
-
     /** 
     @notice Permission-unchecked version of claim creation (no onlyAdmin). Actual logic for create claim, to be run within either createClaim or createClaimBatch.
     @dev This'll simply check the input parameters, and create the structure verbatim based on passed in parameters.
@@ -299,7 +282,6 @@ contract VTVLVesting is Context, AccessProtected {
         // Effects limited to lines below
         claims[_recipient] = _claim; // store the claim
         numTokensReservedForVesting += allocatedAmount; // track the allocated amount
-        vestingRecipients.push(_recipient); // add the vesting recipient to the list
         emit ClaimCreated(_recipient, _claim); // let everyone know
     }

```


## According to test, mint amount = 0 should revert INVALID_AMOUNT

On [VariableSupplyERC20Token.ts#L13](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/test/token/VariableSupplyERC20Token.ts#L13) it says that if you mint(address, 0) it should revert but it doesnt.

Add a require to fix this;
```diff
diff --git a/contracts/token/VariableSupplyERC20Token.sol b/contracts/token/VariableSupplyERC20Token.sol
index 1297a14..ee8c9c1 100644
--- a/contracts/token/VariableSupplyERC20Token.sol
+++ b/contracts/token/VariableSupplyERC20Token.sol
@@ -42,6 +42,7 @@ contract VariableSupplyERC20Token is ERC20, AccessProtected {
             // We need to reduce the amount only if we're using the limit, if not just leave it be
             mintableSupply -= amount;
         }
+        require(amount != 0, "INVALID_AMOUNT");
         _mint(account, amount);
     }
```

## Missing logic on VariableSupplyERC20Token

There is a todo test on ['VariableSupplyERC20Token.ts#L15'](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/test/token/VariableSupplyERC20Token.ts#L15) that says;
```//  9. If mintableSupply > 0, check mint() succeeds with amount == mintableSupply and amount ~= mintableSupply / 2```
But i dont see this logic implemented on [`VariableSupplyERC20Token.sol:mint`](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L36-L46)




# QA

## Unused import `Ownable.sol`

### `AccessProtected.sol`

You are importing `@openzeppelin/contracts/access/Ownable.sol` but not using it, i recommend to remove line (AccessProtected.sol#L5)[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L5]

### `VTVLVesting.sol`

You are importing `@openzeppelin/contracts/access/Ownable.sol` but not using it, i recommend to remove line (VTVLVesting.sol#L6)[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L6]

```diff
diff --git a/contracts/AccessProtected.sol b/contracts/AccessProtected.sol
index 188c0dd..4675965 100644
--- a/contracts/AccessProtected.sol
+++ b/contracts/AccessProtected.sol
@@ -2,8 +2,6 @@
 pragma solidity 0.8.14;
 
 import "@openzeppelin/contracts/utils/Context.sol";
-import "@openzeppelin/contracts/access/Ownable.sol";
-
 /** 
 @title Access Limiter to multiple owner-specified accounts.
 @dev Exposes the onlyAdmin modifier, which will revert (ADMIN_ACCESS_REQUIRED) if the caller is not the owner nor the admin.
diff --git a/contracts/VTVLVesting.sol b/contracts/VTVLVesting.sol
index c564c6f..300bbc5 100644
--- a/contracts/VTVLVesting.sol
+++ b/contracts/VTVLVesting.sol
@@ -3,7 +3,6 @@ pragma solidity 0.8.14;
 // Note: using solidity 0.8, SafeMath not needed any more
 
 import "@openzeppelin/contracts/utils/Context.sol";
-import "@openzeppelin/contracts/access/Ownable.sol";
 import "@openzeppelin/contracts/interfaces/IERC20.sol";
 import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
 import "./AccessProtected.sol";
```

## Solve and Remove TODOs

on [VTVLVesting.sol#L266](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266)
`// Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?`

## Remove dead code

You have some commented code on;

[VTVLVesting.sol#L113-L115](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L113-L115)
[VTVLVesting.sol#L133-L138](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L133-L138)
[VTVLVesting.sol#L261](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L261)
[token/FullPremintERC20Token.sol#L9](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L9)