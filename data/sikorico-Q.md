# QA REPORT

## [QA 00] Timelock is missing for the following functions


### Proof of concept:
- [AccessProtected.sol#L39](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L39)

## [QA 01] Missing MIT licence for the following contracts


### Proof of concept:
- [TestERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/test/TestERC20Token.sol)
- [FullPremintERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/FullPremintERC20Token.sol)

## [QA 02] Not emitted event for state changes


### Proof of concept:
- [VTVLVesting.sol#L81](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L81)
- [VariableSupplyERC20Token.sol#L21](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L21)

## [QA 03] Remove the name from the following unused function parameters


### Proof of concept:
- [VariableSupplyERC20Token.sol#L21](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L21)
- [FullPremintERC20Token.sol#L10](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/FullPremintERC20Token.sol#L10)
- [TestERC20Token.sol#L8](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/test/TestERC20Token.sol#L8)
