## Low

1. There is a comment on line 203 suggesting that the function finalVestedAmount() is unneccessary and may be removed, to prevent revokeClaim() from reverting if this happens I recommend updating line 422 to: 
```
uint112 finalVestAmt = vestedAmount(_recipient, _claim.endTimestamp);
```
[VTVLVesting.sol#L203](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L203) 
[VTVLVesting.sol#L422](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L422) 

2. Recommend locking pragma to the version used in testing. 
[VariableSupplyERC20Token.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L2) 

## Non Critical

1. Recommend removing open todos before deployment.
[VTVLVesting.sol#L266](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266) 

2. Natspec is incomplete in the following places.
[VTVLVesting.sol#L416](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L416) - add @param `_recipient` 