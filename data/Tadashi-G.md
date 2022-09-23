### ****Variables with default value do not need to be initialized****

**Summary**: state variable `numTokensReservedForVesting` of VTVLVesting contract and variable `vestAmt` in function `_baseVestedAmount` do not need to be initialized.

**Details**: Perform these changes:

- in [L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27) of VTVLVesting.sol:
    
    ```diff
    -uint112 public numTokensReservedForVesting = 0;
    +uint112 public numTokensReservedForVesting;
    ```
    
- in [L148](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148) of VTVLVesting.sol:
    
    ```diff
    -uint112 vestAmt = 0;
    +uint112 vestAmt;
    ```
    

⛽ **Profiling**:

By performing these changes the average gas used (computed using th`npx hardhat test`) can be decreased:  

- `revokeClaim()`: 40827 → 40819
- `withdraw()`: 72938 → 72930
- Deployment of `VTVLVesting`: 3740739 → 3737632

### **Post-increments that can be replaced by pre-increments**

**Summary**: Pre-incrementing a variable is cheaper than post-incrementing it. For more information, see [G012](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g012---use-prefix-increment-instead-of-postfix-increment-if-possible) of c4-common-issues. 

**Details**: 
Consider the following change to [L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353) of VTVLVesting.sol:

```diff
-for (uint256 i = 0; i < length; i++) {
+for (uint256 i = 0; i < length; ++i) {
```

⛽ **Profiling**:

By performing these changes the average gas used (computed using th`npx hardhat test`) can be decreased:  

- `revokeClaim()`: 40827 → 40819
- `createClaim()`: 167760 → 167759
- `createClaimsBatch()`: 284486 → 284476
- Deployment of `VTVLVesting`: 3740739 → 3740295