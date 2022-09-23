
**Incorrect comments**
The comment [`max supply == 0 means mint at will.`](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L22) should be `maxSupply_ == 0 means mint at will.`

The comment [`However, if both are 0 we might have a valid scenario as well - user just wants to create a token but doesn't want to mint anything`](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L25) is incorrect. If both `maxSupply_` and `initialSupply_` are 0 then the user just wants to create a token but doesn't want to mint anything INITIALLY. It does indeed seem that this should be allowed, in which case the entire require-statement at line 27 can be removed.

&nbsp;

**Compiler version should be fixed**

Instances:
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L2