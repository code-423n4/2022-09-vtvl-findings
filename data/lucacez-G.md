### Don't Initialize Variables with Default Value
Uninitialized variables are assigned with the types default value.
Explicitly initializing a variable with it's default value costs unnecesary gas.

There are 2 instances of this issue:

```
File: contracts\VTVLVesting.sol

uint112 vestAmt = 0;
```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148
```
File: contracts\VTVLVesting.sol

for (uint256 i = 0; i < length; i++) {
```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353

### COMPARISONS: != IS MORE EFFICIENT THAN > IN REQUIRE (6 GAS LESS)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas).
For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check > 0 is essentially checking that the value is not equal to 0 therefore >0 can be replaced with !=0 which saves gas.

Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

I suggest changing > 0 with != 0 here:

File: VTVLVesting.sol [Line 107](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107)
```
require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
```
File: VTVLVesting.sol [Line 256](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256)
```
require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
```
File: VTVLVesting.sol [Line 257](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L257)
```
require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
```

File: VTVLVesting.sol [Line 263](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263)
```
require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
```

File: VTVLVesting.sol [Lines 272-273](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L272-L273)
```
_cliffReleaseTimestamp > 0 &&
_cliffAmount > 0 &&
```

File: VTVLVesting.sol [Line 449](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449)
```
require(bal > 0, "INSUFFICIENT_BALANCE");
```

File: token\FullPremintERC20Token.sol [Line 11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11)
```
require(supply_ > 0, "NO_ZERO_MINT");
```

File: token\VariableSupplyERC20Token.sol [Line 27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27)
```
require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```

File: token\VariableSupplyERC20Token.sol [Line 31](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L31)
```
if(initialSupply_ > 0) {
```

File: token\VariableSupplyERC20Token.sol [Line 40](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L40)
```
if(mintableSupply > 0) {
```