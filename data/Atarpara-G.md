
### Gas Optimization



#### G-01 No need to assign zero declaration time

As per ethereum yellow [paper](https://ethereum.github.io/yellowpaper/paper.pdf) paid 2900 for an SSTORE operation when the storage value’s zeroness remains unchanged or
is set to zero 

File: VTVLVesting.sol [Line-27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L334-L27)

```
uint112 public numTokensReservedForVesting = 0; 
```


#### G-02 Convert memory to calldata type into function parameter

Convert memory type to calldata for avoid unnecessary copy into memory

File: VTVLVesting.sol [Line-334](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L334-L340)


The code would go from:

```
 function createClaimsBatch(
        address[] memory _recipients, 
        uint40[] memory _startTimestamps, 
        uint40[] memory _endTimestamps, 
        uint40[] memory _cliffReleaseTimestamps, 
        uint40[] memory _releaseIntervalsSecs, 
        uint112[] memory _linearVestAmounts, 
        uint112[] memory _cliffAmounts) 
        external onlyAdmin {}

```
To 

```
function createClaimsBatch(
        address[] calldata _recipients, 
        uint40[] calldata _startTimestamps, 
        uint40[] calldata _endTimestamps, 
        uint40[] calldata _cliffReleaseTimestamps, 
        uint40[] calldata _releaseIntervalsSecs, 
        uint112[] calldata _linearVestAmounts, 
        uint112[] calldata _cliffAmounts) 
        external onlyAdmin {}
```

#### G-03 Increment can be unchecked
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.


File: VTVLVesting.sol [Line-353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)

The code would go from:

```
for (uint256 i=0; i < length; ++i) {  
 // ...  
}  
```

to 
```
for (uint256 i; i < length;) {  
 // ...  
 unchecked{
     ++i;
 }
}  
```

#### G-04 Convert multiple condition into one condition


File : VariableSupplyERC20Token.sol [Line-27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27)

In VariableSupplyERC20Token constructor requirement condition can be packed into one condition for the save deploytime gas cost and tiny bytecode size


The code would go from:
```
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```
To
```
require((initialSupply | maxSupply_) != 0, "INVALID_AMOUNT");
```


#### G-05 > 0 is less efficient than != 0 for unsigned integer

!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)

Instance Inclued : 

File : VTVLVesting.sol 
[Line-107](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107)
[Line-256](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256)
[Line-257](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257)
[Line-263](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263)
[Line-272-273](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272-273)
[Line-449](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449)


File : VariableSupplyERC20Token.sol 
[Line-27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27)