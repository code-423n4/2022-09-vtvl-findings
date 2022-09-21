# QA REPORT

## [QA 00] Two steps verification process is missing.
The process of transferring ownership is risky because typing the wrong address can have serious consequences.

### Proof of concept:
- [AccessProtected.sol](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol)

## [QA 01] In the following places, use the multiplication math operators before the divisions.


### Proof of concept:
- [VTVLVesting.sol#L168](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L168)

## [QA 02] Consider adding to the following contracts an MIT license


### Proof of concept:
- [FullPremintERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/FullPremintERC20Token.sol)
- [TestERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/test/TestERC20Token.sol)

## [QA 03] Add Timelock for the following functions:


### Proof of concept:
- [AccessProtected.sol#L39](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L39)
