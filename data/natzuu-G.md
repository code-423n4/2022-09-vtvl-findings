# G001 - Don't Initialize Variables with Default Value

### Description

Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

```solidity
uint112 vestAmt = 0;
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148)

```solidity
for (uint256 i = 0; i < length; i++) {
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)

### background info

[https://mudit.blog/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size/](https://mudit.blog/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size/)

## G002 - `++I` COSTS LESS GAS COMPARED TO `I++` OR `I += 1` (SAME FOR `--I` VS `I--` OR `I -= 1`)

Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

```solidity
for (uint256 i = 0; i < length; i++) {
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)

# G003 - `++I`/`I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN `FOR`AND `WHILE`LOOPS

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

```solidity
for (uint256 i = 0; i < length; i++) {
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)

# G004 - USING `> 0` COSTS MORE GAS THAN `!= 0` WHEN USED ON A `UINT` IN A `REQUIRE()` STATEMENT

This change saves **[6 gas](https://aws1.discourse-cdn.com/business6/uploads/zeppelin/original/2X/3/363a367d6d68851f27d2679d10706cd16d788b96.png)** per instance

```solidity
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107)

```solidity
require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256)

```solidity
require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L257](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L257)

```solidity
require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263)

```solidity
_cliffReleaseTimestamp > 0 &&
_cliffAmount > 0 &&
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L272](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L272)

```solidity
require(bal > 0, "INSUFFICIENT_BALANCE");
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449)

```solidity
require(supply_ > 0, "NO_ZERO_MINT");
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11)

```solidity
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```

[https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27)