
## Summary

G-01 Unnecessary Use of _msgSender() (2 instances)
G-02 Splitting require() statements that use && saves gas (3 instances)
G-03 ++i costs less gas compared to i++ (1 instance)
G-04 Using calldata instead of memory for read-only arguments in external functions saves gas (1 instance)
G-05 <x> += <y> costs more gas than <x> = <x> + <y> for state variables  (4 instances)
G-06 ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops (1 instance)
G-07 Using bools for storage incurs overhead  (1 instance)
G-08 Use != 0 instead of > 0 (6 instances)
G-09 Redundant zero initialization  (1 instance)
G-10 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead (50 instances)
G-11 Use custom errors rather than revert()/require() strings to save deployment gas (17 instances)
G-12 Public functions to external (3 instances)
G-13 Use calldata instead of memory for function parameters (8 instances)
G-14 Use simple comparison in trinary logic / in if statement (3 instances)

Total: 101 instances in 14 issues


## G-01 Unnecessary Use of _msgSender()

The use of _msgSender() when there is no implementation of a meta transaction mechanism that uses it, such as EIP-2771, very slightly increases gas consumption.

Recommended Mitigation Steps:
Replace _msgSender() with msg.sender if there is no mechanism to support meta-transactions like EIP-2771 implemented.

2 instances in 2 files:

FullPremintERC20Token.sol
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L12

VariableSupplyERC20Token.sol
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L32



## G-02 Splitting require() statements that use && saves gas

Instead of using the && operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement (saving 3 gas per &).

8 instances in 1 file:

VTVLVesting.sol
https://gitVTVLVesting.solhub.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272-L273 (2)
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L276
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L348 (5)


## G-03 ++i costs less gas compared to i++

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). 

1 instance in 1 file:

VTVLVesting.sol
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353


## G-04 Using calldata instead of memory for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

1 instance in 1 file:

VTVLVesting.sol
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L334-L340 (7)


## G-05 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

4 instances:

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L161
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L179
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381


## G-06 ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

1 instance in 1 file:

VTVLVesting.sol
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353


## G-07 Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. 

This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled. https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use uint256(1) and uint256(2) for true/false

1 instance in 1 file:

AccessProtected.sol
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L12


## G-08 Use != 0 instead of > 0

Using > 0 uses slightly more gas than using != 0. Use != 0 when comparing uint variables to zero, which cannot hold values below zero. This change saves 6 gas per instance

6 instances in 1 file:

VTVLVesting.sol
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L273
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449


## G-09 Redundant zero initialization 

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas. There are several places where an int is initialized to zero, which looks like: uint256 amount = 0; 

Recommended Mitigation Steps: 
Remove the redundant zero initialization uint256 amount; ( / or: If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.)

1 instance:

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27


## G-10 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. 

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 

Use a larger size then downcast where needed

50 intances in file:

VTVLVesting.sol
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L35
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L36
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L37
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L38
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L42
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L43
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L44
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L64
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L69
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L74
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L147
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L167
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L169
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L170
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L176
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L196
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L206
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L215
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L217
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L230
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L247
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L248
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L249
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L250
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L251
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L252
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L292
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L319
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L320
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L321
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L322
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L323
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L324
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L335
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L336
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L337
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L338
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L339
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L340
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L343
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L371
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L377
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L422
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L429
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L436
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L448


## G-11 Use custom errors rather than revert()/require() strings to save deployment gas

Custom errors are available from solidity version 0.8.4. The instances below match or exceed that version.

17 instances:


https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L82
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L255
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L262
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L264
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L278
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L350
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L374
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L402
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L447
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449



## G-12 Public functions to external

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 
https://docs.soliditylang.org/en/latest/contracts.html#function-overriding

3 instances:

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L196
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L206
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398



## G-13 Use calldata instead of memory for function parameters

Having function arguments use calldata instead of memory can save gas. 

8 instances:

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L147
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L334
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L335
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L336
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L337
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L338
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L339
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L340


## G-14 Use simple comparison in trinary logic / in if statement (>=)

The comparison operators >= and <= use more gas than >, <, or ==. Replacing the >= and ≤ operators with a comparison operator that has an opcode in the EVM saves gas. 

Recommended Mitigation Steps: Replace the comparison operator and reverse the logic to save gas using the suggestions above.

3 instances

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L160
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L402

