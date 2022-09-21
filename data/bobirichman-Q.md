# QA REPORT

## [LOW] Missing pause functionality


Example: [VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol)

## [LOW] Use mult before div
To improve the following calculations precision consider changing the order of the operations such that multiplications come before divisions.

Example: [VTVLVesting.sol#L168](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L168)

## [NON CRITICAL] Consider emitting an event at the following functions


### Proof of concept:
- [VariableSupplyERC20Token.sol#L21](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L21)
- [VTVLVesting.sol#L81](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L81)

## [NON CRITICAL] Missing function spec comments


### Proof of concept:
- [VariableSupplyERC20Token.sol#L21](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L21)
- [VTVLVesting.sol#L230](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L230)

## [NON CRITICAL] Floating pragma
Floating pragma is a bad practice, since it does not guaranty the same version at future deployments.

### Proof of concept:
- [TestERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/test/TestERC20Token.sol)
- [VariableSupplyERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol)
