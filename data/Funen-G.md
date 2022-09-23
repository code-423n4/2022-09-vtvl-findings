1. Custom Error 

Custom errors can be used from Solidity 0.8.4 are cheaper than revert strings. Its cheaper deployment cost and runtime cost when the revert condition is met.

```
/contracts/token/FullPremintERC20Token.sol#L11
/contracts/token/VariableSupplyERC20Token.sol#L27
/contracts/token/VariableSupplyERC20Token.sol#L37
/contracts/token/VariableSupplyERC20Token.sol#L41
/contracts/AccessProtected.sol#L25
/contracts/AccessProtected.sol#L40
```

2. Rather than `public` use `external` instead for saving more gas

1.) https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39

3.  use `++i` for saving more gas

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353 (++i)

4. `uint256 i = 0` changed to `uint256 i` for cost less gas 

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353

5. Use `||` rather than `&&` can saving gas

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L350

This implementation down below can saving gas per iteration and since it was use a lot, it should be save lot of gas

```
        require(_startTimestamps.length == length ||
                _endTimestamps.length == length ||
                _cliffReleaseTimestamps.length == length ||
                _releaseIntervalsSecs.length == length ||
                _linearVestAmounts.length == length ||
                _cliffAmounts.length == length, 
                "ARRAY_LENGTH_MISMATCH"
        );  
// gas deployment
//2223531 after (||)
//2224732 before (&&)
```