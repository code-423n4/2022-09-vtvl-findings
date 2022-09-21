# GAS REPORT

## [GAS] The following require statements could be split into multiple require statements to save gas


### Proof of concept:
- [VTVLVesting.sol#L343](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L343)
- [VTVLVesting.sol#L269](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L269)

## [GAS] Consider using custom errors
You can utilize customized errors in require statements to save gas.

### Proof of concept:
- [VariableSupplyERC20Token.sol#L36](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L36)
- [VariableSupplyERC20Token.sol#L40](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L40)

## [GAS] Caching msg.sender is unnecessary


### Proof of concept:
- [AccessProtected.sol#L17](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L17)
- [VTVLVesting.sol#L432](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L432)
