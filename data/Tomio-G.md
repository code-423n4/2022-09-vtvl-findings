1.
Title: Comparison operators

Proof of Concept:
[VTVLVesting.sol#L160](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L160)
[VTVLVesting.sol#L295](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295)
[VTVLVesting.sol#L402](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L402)

Recommended Mitigation Steps:
Replace `<=` with `<`, and `>=` with `>` for gas optimization
________________________________________________________________________

2.
Title: Custom errors from Solidity 0.8.4 are cheaper than revert strings

Impact:
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information

Custom errors are defined using the error statement
reference: https://blog.soliditylang.org/2021/04/21/custom-errors/

Proof of Concept:
[VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol) (various line)
[VariableSupplyERC20Token.sol#L41](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L41)

Recommended Mitigation Steps:
Replace require statements with custom errors.
________________________________________________________________________

3.
Title: Using `!=` in `require` statement is more gas efficient

Proof of Concept:
[VTVLVesting.sol#L107](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107)
[VTVLVesting.sol#L256-L257](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256-L257)
[VTVLVesting.sol#L263](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263)
[VTVLVesting.sol#L272-L273](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272-L273)
[VTVLVesting.sol#L449](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449)

Recommended Mitigation Steps:
Change `> 0` to `!= 0`
________________________________________________________________________

4.
Title: Default value initialization

Impact:
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Proof of Concept:
[VTVLVesting.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27)
[VTVLVesting.sol#L148](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L148)
[VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)

Recommended Mitigation Steps:
Remove explicit initialization for default values.
________________________________________________________________________

5.
Title: Using unchecked and prefix increment is more effective for gas saving:

Proof of Concept:
[VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)

Recommended Mitigation Steps:
Change to:

```
	for (uint256 i = 0; i < length;) {
    // ...
    unchecked { ++i; }
    }
```
________________________________________________________________________

6.
Title: Using multiple `require` instead `&&` can save gas

Proof of Concept:
[VTVLVesting.sol#L270-L278](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L270-L278)
[VTVLVesting.sol#L344-L351](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344-L351)

Recommended Mitigation Steps:
Change to:

```
	require(_startTimestamps.length == length, "ARRAY_LENGTH_MISMATCH");
        require(_endTimestamps.length == length, "ARRAY_LENGTH_MISMATCH");
        require(_cliffReleaseTimestamps.length == length, "ARRAY_LENGTH_MISMATCH");
        require(_releaseIntervalsSecs.length == length, "ARRAY_LENGTH_MISMATCH");
        require(_linearVestAmounts.length == length, "ARRAY_LENGTH_MISMATCH");
        require(_cliffAmounts.length == length, "ARRAY_LENGTH_MISMATCH");
```
________________________________________________________________________

7.
Title: Boolean comparison

Proof of Concept:
[VTVLVesting.sol#L111](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111)

Recommended Mitigation Steps:
Using `== true` to validate bool variable is unnecessary:
Change to: 

```
        require(_claim.isActive, "NO_ACTIVE_CLAIM");
```
________________________________________________________________________

8.
Title: `>=` is cheaper than `>`

Impact:
Strict inequalities (`>`) are more expensive than non-strict ones (`>=`). This is due to some supplementary checks (ISZERO, 3 gas)

Proof of Concept:
[VTVLVesting.sol#L187](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L187)

Recommended Mitigation Steps:
Consider using `>=` instead of `>` to avoid some opcodes
________________________________________________________________________

9.
Title: Unchecking arithmetics operations that can't underflow/overflow 

Proof of Concept:
[VTVLVesting.sol#L167](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L167) L#167 should be unchecked due to L#166
[VTVLVesting.sol#L377](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L377) L#377 should be unchecked due to L#374
[VTVLVesting.sol#L429](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L429) L#429 should be unchecked due to L#426

Recommended Mitigation Steps:
Use `unchecked`
________________________________________________________________________