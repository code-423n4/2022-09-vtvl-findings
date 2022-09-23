# Report
## Gas Optimizations ## 

### [G-01]: X += Y (X -= Y) costs more gas than X = X + Y (X = X - Y) 
**Context:** 

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L433

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L161

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L179

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301

**Recommendation:**

Change X += Y (X -= Y) to X = X + Y (X = X - Y).

### [G-02]: Don't initialize variable with its default value 
**Context:** 

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

**Description:**

Default value of uint is 0. It's unnecessary and costs more gas to initialize uint variavles to 0.

**Recommendation:**

+ Change uint112 vestAmt = 0; to uint112 vestAmt

+ Change uint256 i = 0; to uint256 i;


### [G-03]: >0 costs more gas than !=0 
**Context:** 

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L273

**Description:**

uint256, uint112, uint96, uint40 type will never be less than 0.

**Recommendation:**

Change >0 to !=0.


### [G-04]: i++ costs more gas than ++i
**Context:**

```
for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

**Recommendation:**

Change i++ to ++i.


### [G-05]: Unchecked arithmetic
**Context:**

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L167

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L170

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L377

+ https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L429

**Description:**

Some gas can be saved by using an unchecked {} block if an overflow/underflow isn't possible because of a previous require() or if-statement.

**Recommendation:**

Place the arithmetic operations in an unchecked block.
