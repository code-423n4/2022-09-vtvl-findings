1. Missmatch `initialSupply_` check

In this down below was the [comment section](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L30) that declared  :

```
        // Note: the check whether initial supply is less than or equal than mintableSupply will happen in mint fn.
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L31

if condition of `initialSupply_ > 0` doesn't declared  before if is less than or equal than mintableSupply. so you can be set an require before `if` condition `initialSupply_` <= `mintableSupply`. 

2. Need to check amount of `_amountRequested`

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398-L411

Should check the mininum of `_amountRequested` , the example `!= 0` or `> 0` eversince the amount can't be zero so it would be insufficient balance

3. `_initialSupply` didn't declared 

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L9

`_initialSupply` can be declared inside that contract (which was used for comment) or can be declare on :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

4. `_initialSupply` can be declared as 1e18

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L9

if `_initialSupply` was declared, it can be changed to be `1e18` to represents the number of decimals to your token.

Since it was `100 * 1e18` it can be direct declare to `1e20` or us the same as it was `100 * 1e18`

5.  Consider making contract Pausable 

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

There are many external risks, on that contract for some fn. so my suggestion is that you should consider making the contracts pausable, so in case of an unexpected event, admin can pause transfers.

6.  Locked Pragma Compiler

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

Since it was used ^0.8.14. As the compiler can be use for example 0.8.14 and consider locking at this version the same as another. It can be consider using  locking the pragma version whenever possible and avoid using a floating pragma in the final deployment. Since it can be problematic, if there are publicly disclosed bugs and issues that affect the current compiler version used.

7. Unnecessary Comment

This comment can be deleted before deployement for better use

1.) https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L12

```
// user address => admin? mapping
```

2.) https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L297

```
        // Done with checks
```


