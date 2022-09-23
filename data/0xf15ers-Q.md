### 1. Fee-on-transfer tokens are not considered
- In [contracts/VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol)
- If the `tokenAddress` is a token that takes fees on transfer then the accounting of `numTokensReservedForVesting`  might break. 

### 2. If there is a single admin and it calls `setAdmin()` with it's address and `isEnabled` to false. This will break the functionalities of the conract. 
- https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39-L43 
- Consider adding checks so that there is at least one enabled admin


### 3. While creating claim only for cliff release, it is still required to provide valid start_time and end_time. 
- In [contracts/VTVLVesting.sol#L257-L264](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L257-L264)
- The `createClaim` function still checks for valid start and end time while creating the claim with cliff release only

### 4. Unused imports 
- https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L5 
- `Ownable` is imported but not used

### 5. Unbounded loop can cause out of gas and the function could revert. 
- It is recommended to create a safe limit to create a batch claims. 
- https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L333 
