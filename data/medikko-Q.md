### Recommend to use specific compiler version when contract is not library 

Unspecific version it matters for libraries but for not libraries contracts it is unnecessary. Can cause a security risk of contract

_There are **1** instances of this issue:_

File: VariableSupplyERC20Token line 2

```
pragma solidity ^0.8.14;
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L2