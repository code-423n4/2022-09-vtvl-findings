
## 1. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (same with -=)

- [VTVLVesting.sol#L301](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301)
- [VTVLVesting.sol#L381](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L381)
- [VTVLVesting.sol#L383](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L383)
- [VTVLVesting.sol#L433](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L433)

- [VariableSupplyERC20Token.sol#L43](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43)


## 2. `++i` costs less gas than `i++`, especially when it’s used in for-loops (--i/i-- too)
Saves 6 gas per loop

- [VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)


## 3. it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied

- [VTVLVesting.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27)
- [VTVLVesting.sol#L148](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148)
- [VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)


## 4. ` ++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)


## 5. using > 0 costs more gas than != 0 when used on a uint in a require() statement

- [FullPremintERC20Token.sol#L11](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11)

- [VariableSupplyERC20Token.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27)

- [VTVLVesting.sol#L103](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L103)
- [VTVLVesting.sol#L256](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256)
- [VTVLVesting.sol#L257](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257)
- [VTVLVesting.sol#L263](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263)
- [VTVLVesting.sol#L270](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270)
- [VTVLVesting.sol#L449](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449)


## 6. splitting require() statements that use `&&` saves gas

- [VTVLVesting.sol#L270](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270)
- [VTVLVesting.sol#L344](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344)


## 7. use custom errors rather than revert()/require() strings to save deployment gas

https://blog.soliditylang.org/2021/04/21/custom-errors/

all the `require`s should use this method


## 8. using `calldata` instead of `memory` for read-only arguments in external functions saves gas

7 instances here:

- [VTVLVesting.sol#L334-340](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L334)


## 9. using `bool` for storage incurs overhead

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

- [AccessProtected.sol#L12](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L12)
- [AccessProtected.sol#L39](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39)

- [VTVLVesting.sol#L45](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L45)


## 10. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [VTVLVesting.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27)
- [VTVLVesting.sol#L148](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148)
- [VTVLVesting.sol#L176](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L176)
- [VTVLVesting.sol#L251](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L251)
- [VTVLVesting.sol#L252](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L252)
- [VTVLVesting.sol#L292](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L292)
- [VTVLVesting.sol#L323](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L323)
- [VTVLVesting.sol#L324](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L324)
- [VTVLVesting.sol#L339](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L339)
- [VTVLVesting.sol#L340](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L340)
- [VTVLVesting.sol#L371](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L371)
- [VTVLVesting.sol#L377](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L377)
- [VTVLVesting.sol#L398](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398)
- [VTVLVesting.sol#L422](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L422)
- [VTVLVesting.sol#L429](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L429)

- [VTVLVesting.sol#L147](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L147)
- [VTVLVesting.sol#L167](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L167)
- [VTVLVesting.sol#L169](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L169)
- [VTVLVesting.sol#L170](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L170)
- [VTVLVesting.sol#L196](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L196)
- [VTVLVesting.sol#L247](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L247)
- [VTVLVesting.sol#L248](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L248)
- [VTVLVesting.sol#L249](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L249)
- [VTVLVesting.sol#L250](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L250)
- [VTVLVesting.sol#L319](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L319)
- [VTVLVesting.sol#L320](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L320)
- [VTVLVesting.sol#L321](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L321)
- [VTVLVesting.sol#L322](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L322)
- [VTVLVesting.sol#L335](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L335)
- [VTVLVesting.sol#L336](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L336)
- [VTVLVesting.sol#L337](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L337)
- [VTVLVesting.sol#L338](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L338)
