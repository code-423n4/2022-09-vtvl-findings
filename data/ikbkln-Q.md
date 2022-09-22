# [LOW-01] createClaimsBatch: possible block gas limit reached

## Description

The `createClaimsBatch` ([L333](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L333)) function performs a for-loop on a set of dynamic arrays with unrestricted length. This could trigger the block gas limit for long lists of recipients thus reverting the transaction.

## Recommendations

Consider adding `uint MAX_BATCH_SIZE immutable` to the logic of the contract.

# [INFO-01] AccessProtected.sol: OpenZeppelin's Ownable not used

## Description

Self-explanatory.
