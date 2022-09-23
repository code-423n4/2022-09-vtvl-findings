# Gas Optimizations Report for VTVL Finance contest

## Overview
During the audit, 6 gas issues were found.

№ | Title | Instance Count
--- | --- | --- 
G-1 | [Postfix increment](#g-1-postfix-increment) | 1
G-2 | [Initializing variables with default value](#g-2-initializing-variables-with-default-value) | 3
G-3 | [> 0 is more expensive than =! 0](#g-3--0-is--more-expensive-than--0) | 11
G-4 | [x += y is more expensive than x = x + y](#g-4-x--y-is--more-expensive-than-x--x--y) | 7
G-5 | [Using unchecked blocks saves gas](#g-5-using-unchecked-blocks-saves-gas) | 1
G-6 | [Custom errors may be used](#g-6-custom-errors-may-be-used) | 24

## Gas Optimizations Findings (6)
### G-1. Postfix increment
##### Description
Prefix increment costs less gas than postfix.

##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353

##### Recommendation
Consider using prefix increment  where it is relevant. 

#
### G-2. Initializing variables with default value
##### Description
It costs gas to initialize integer variables with 0 or bool variables with false but it is not necessary.

##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

##### Recommendation
Remove initialization for default values.  
For example:
``` for (uint256 i; i < array.length; ++i) {```

#
### G-3. ```> 0``` is  more expensive than ```=! 0```
##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L273
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449

##### Recommendation
Use ```=! 0``` instead of ```> 0```, where possible.

#
### G-4. ```x += y``` is  more expensive than ```x = x + y```
##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L161
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L179
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L383
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L433

##### Recommendation
Use ```x = x + y``` instead of ```x += y```.
Use ```x = x - y``` instead of ```x -= y```.

#
### G-5. Using unchecked blocks saves gas
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible, some gas can be saved by using unchecked blocks.

##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

#
### G-6. Custom errors may be used
##### Description
[Custom errors from Solidity 0.8.4 are cheaper than revert strings.](https://blog.soliditylang.org/2021/04/21/custom-errors/)

##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L37
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L41
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L25
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L40
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L82
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L129
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L255
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L262
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L264
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L374
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L402
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L447
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449

##### Recommendation
For example, change:
```
require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
```
to:
```
error InvalidAddress();
...
if (address(_tokenAddress) == address(0)) revert InvalidAddress();
```