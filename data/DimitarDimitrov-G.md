# 1. There are many places where require is used.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L25
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L40
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L82
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L129
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255-L257
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L262-L264
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270-L278
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L351
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L402
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L41

Optimize this using revert and custom errors combination. This will save a lot of gas.

# 2. Use memory instead storage: 

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L106
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L124
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L197

Special case:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L367
if this become `Claim memory usrClaim = claims[_msgSender()];` this https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381 should become `claims[_msgSender()].amountWithdrawn += amountRemaining;`

Same for: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L420

# 3. Can use calldata instead memory
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L147
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L334-L340
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L21

# 4. Good practice

## 4.1. Use one version of solidity compiler
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L2 0.8.14
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L2 0.8.14
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L2 0.8.14
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2 ^0.8.14

Migrate all to 0.8.14

## 4.2. Do not copy code
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L17-L18

In this case you can use setAdmin(_msgSender(), true);