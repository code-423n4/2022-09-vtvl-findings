Table Of Content
================

* [GAS REPORT](#gas-report)
        * [Don't cache msg.sender](#dont-cache-msgsender)
        * [Split require statement with & operator](#split-require-statement-with--operator)

# GAS REPORT

## Don't cache msg.sender
reading msg.sender is 2 gas units which is less than a read of a local var + the unnecessary store operation.

### Code Instances:
- [VTVLVesting.sol#L271](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L271)
- [VTVLVesting.sol#L260](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L260)
- [VTVLVesting.sol#L341](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L341)
- [VariableSupplyERC20Token.sol#L27](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L27)

## Split require statement with & operator
instead of require(A & B, ...) consider having two require statements require(A, ...) and require(B, ...) for better code quality and improved gas usage.

### Code Instances:
- [VTVLVesting.sol#L269](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L269)
- [VTVLVesting.sol#L343](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L343)
