## 1 Use of custom errors
Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

# Code references
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L40
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L82
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107-111
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L129
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L255-270
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L294
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L374
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L402
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L447-449
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L41

# Mitigation
Replace require() with revert ERROR()

## 2 Unchecked block
Use of unchecked block in loops for arithmetic ops - to avoid overflow checks
Solidity version 0.8+ comes with an implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for a loop. eg.

# Code references:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

# Mitigation

for(uint256 i; i < 10; i++){
//doSomething
}

can be written as shown below.

for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}

## 3 Multiple conditions in single statement

Instead of using the && operator in a single require statement to check multiple conditions,using multiple if statements with 1 condition per require statement will save roughly 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

# Code references:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272-277
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344

# Mitigation
Use multiple if conditions and revert ERROR()

## 4 Use of != iinstead of >0
While comparing uint variables, better to use != operator. It's much more gas efficient

# Code references:
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272-274
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256-257
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11

## 4 Duplicate initialization of uint variables
unit variables are by default initialized to 0. Assigning them 0 again can cost extra gas

# Code references
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148
