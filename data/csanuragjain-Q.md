## Incorrect condition may prohibit valid scenario

Contract:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27

Issue:
In the constructor of VariableSupplyERC20Token.sol, it is always possible to have both initialSupply_ and maxSupply_ equal to zero (Admin allows unlimited minting with initial 0 tokens). However this is not possible due to below check

```
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```

Recommendation:
Remove the below check

```
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```