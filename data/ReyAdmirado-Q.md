## 1. use of floating pragma

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

- [VariableSupplyERC20Token.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2)


## 2. event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

- [VTVLVesting.sol#L69](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L69)


## 3. _safemint() should be used rather than _mint() wherever possible

- [FullPremintERC20Token.sol#L12](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L12)

- [VariableSupplyERC20Token.sol#L45](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L45)


## 4. lines are too long

Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

- [VTVLVesting.sol#L235](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L235)
- [VTVLVesting.sol#L241](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L241)
- [VTVLVesting.sol#L256](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256)
- [VTVLVesting.sol#L260](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L260)
- [VTVLVesting.sol#L269](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L269)
- [VTVLVesting.sol#L313](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L313)
- [VTVLVesting.sol#L354](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L354)
- [VTVLVesting.sol#L357](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L357)
- [VTVLVesting.sol#L432](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L432)


## 5. open todo
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

- [VTVLVesting.sol#L266](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L266)
