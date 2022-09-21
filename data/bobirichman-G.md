# GAS REPORT

## [GAS] Mark as payable If has onlyOwner modifier
In order to save gas you can put a payable modifier for functions that are called by protocol owners.

### Proof of concept:
- [VTVLVesting.sol#L341](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L341)
- [AccessProtected.sol#L39](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L39)

## [GAS] Use > instead != to compare uint with 0


### Proof of concept:
- [VTVLVesting.sol#L269](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L269)
- [FullPremintERC20Token.sol#L10](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/FullPremintERC20Token.sol#L10)

## [GAS] Use assembly opcodes iszero in the following locations


### Proof of concept:
- [VTVLVesting.sol#L273](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L273)
- [VTVLVesting.sol#L276](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L276)

## [GAS] Split require statements with ```and``` operator
Split the require statements to improve gas usage.

### Proof of concept:
- [VTVLVesting.sol#L269](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L269)
- [VTVLVesting.sol#L343](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L343)
