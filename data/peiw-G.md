- `++i` costs less gas than `i++`, especially when in FOR loops.
	- `++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration).
	- Instance: 
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

- Using unchecked blocks to save gas - increments in for loop can be unchecked (SAVE 30-40 GAS PER LOOP ITERATION).
	- The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop .
	- Instance: 
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353


-  It costs more gas to initialize NON-CONSTANT/NON-IMMUTABLE variables to zero than to let the default of zero be applied.
	- Instance: 
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27


- Usage of `UINTS/INTS` smaller than 32 bytes incurs overhead.
	-  When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed.
	- Instances:
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L176


- `!=` is more efficient than `>` in `require()`.
	- Instance:
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11

- Use custom errors rather than `REVERT()/REQUIRE()` strings to save gas.
	- Custom errors are available from solidity version 0.8.4. Custom errors save \~50 gas each time, they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.
	- Instance:
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L82


- Splitting `REQUIRE()` statements that use `&&` saves gas.
	- Instance:
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344


- `x += y` cost more gas than `x = x + y` for state variables.
	- Instance:
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L179
		- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L301
