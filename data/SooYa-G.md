## > 0 is less efficient than != 0 for unsigned integers 
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)

Proof: While it may seem that > 0 is cheaper than !=, 
this is only true without the optimizer enabled and outside a require statement. 
If you enable the optimizer at 10k AND you’re in a require statement, 
this will save gas. You can see this tweet for more proofs:
https://twitter.com/gzeon/status/1485428085885640706
I suggest changing > 0 with != 0 here: 

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107

## Loops can be optimized in several ways
To optimize this loop and make it consume less gas, we can do the folowing things:

1. Use ++i instead of i++, which is a cheaper operation (in this case there is no difference between i++ and ++i because we dont use the return value of this expression, which is the only difference between these two expression).
2. There’s no need to initialize i to its default value, it will be done automatically and it will consume more gas if it will be done (I know, sounds stupid, but trust me - it works).
So after applying all these changes, the loop will look something like this:

```
for (uint256 i; i < Variable; ++i) {
```

I suggest to change the code here:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

## `X += Y` Costs More Gas Than `X = X + Y` For State Variables
Change each operation that using += to `X = X + Y` for state variables
Average gas before changes: 3740739
Average gas after changes: 3740283

There are 7 instances for this issues:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L161
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L179
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L433
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L383

## Declare 'numTokenReservedForVesting;' Instead of `numTokenReservedForVesting = 0;`
There’s no need to initialize variable to its default value, it will be done automatically and it will consume more gas if it will be done. 

Average gas before change: 3740283
Average gas after change: 3737188

There is 1 instance for this issue:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27

## SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS
Instead of using the && operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement (saving 3 gas per &). 

There is 1 instance for this issue:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L351

## Cache Storage Values in Memory To Minimze SLOADS
The code can be optimized by minimizing the number of SLOADs. SLOADs are expensive 100 gas compared to MLOADs/MSTOREs(3gas)
Storage value should get cached in memory

`_claim.amountWithdrawn` should be cached into
``` uint112 amountWithdrawn = _claim.amountWithdrawn; ```

Average gas before caching : 40819
Average gas after caching : 40682

There is 1 instance for this issue 
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L418-L437