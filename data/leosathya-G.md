### [G-01] NO NEED TO EXPLICITLY INITIALIZE VARIABLES WITH DEFAULT VALUES

Here Uints initiaziled with 0, which is not necessary cause its by default value is zero

There is 2 instance of this issue:


> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148

#### Recommended Mitigation

Instead of initializing default leave them as it is.
For ex : 
           Instead of doing uint x = 0;
           leave them as it is uint x;

both are same, second one is more gas optimized.



### [G-02] INSTEAD OF USING DUPLICATE REQUIRE(), LOGIC SHOULD ENCLOSED INSIDE A MODIFIER, AND USED WHENEVER REQUIRED

The require condition require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");  used multiple times

There is 2 Instances of this issue:

> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L82
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L255


#### Recommended Mitigation

This require() statement should enclosed inside a modifier and should used in 




### [G-03] >= COSTS LESS GAS THAN >

There is 4 instance of this issue:

> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L154
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L166
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L374
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449


#### Recommended Mitigation

Instead of using < try to use <= whenever possible




### [G-04] <x> = <x> + <y> is more gas efficient than use of <x> += <y>

In many instances <x> += <y> This type of syntax are used, Those can optimized to previous one i.e  <x> = <x> + <y>

There are 6 instances of this issue:
 
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L161
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L179
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L383
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L433




### [G-05] INSTEAD OF USING && INSIDE REQUIRE(), IT SHOULD BE SPLIT INTO INDIVIDUALS. THIS IS MORE GAS EFFICIENT 

            require(
			_startTimestamps.length == length &&
			_linearVestAmounts.length == length &&
			_cliffAmounts.length == length,
			"ARRAY_LENGTH_MISMATCH"
		);
**TO**
           require(_startTimestamps.length ,"ARRAY_LENGTH_MISMATCH");
           require(_linearVestAmounts.length ,"ARRAY_LENGTH_MISMATCH");
           require(_cliffAmounts.length ,"ARRAY_LENGTH_MISMATCH");

There are 2 instances of this issue:

> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L350
> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270-L278




### [G-06] ] SHOULD OPTIMIZED FOR LOOP

There is 1 instance of this issue: 

> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353-L355

#### Recommended Mitigation

. Should not initialize uint with default value i.e uint i=0 **TO** uint i;
. Should use ++i instead i++
. Should uncheck i++





