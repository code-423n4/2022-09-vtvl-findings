## Unused imports of `Ownable.sol`

### Affected Contracts

- AccessProtected.sol#5
- VTVLVesting.sol#6

### Description

The contracts mentioned above import the Ownable.sol OZ contract without using it.

The `AccessProtected.sol` implements its own access control functionality and it doesn’t inherit from `Ownable.sol` neither it used any of its functions.

The `VTVLVesting.sol` inherits `AccessProtected.sol` and uses its access control logic. There is no need to import `Ownable.sol` since it doesn’t use any of its functions or modifiers.

### Mitigation

To improve readability and avoid confusion, consider removing unused imports.


## Gas Optimization in `VTVLVesting.sol` (`withdraw` function)

### Affected Functions

- VTVLVesting.sol#374
    - withdraw() ([https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374))

### Description

The `withdraw` function reverts in case `allowance > usrClaim.amountWithdrawn`, in case `allowance = usrClaim.amountWithdrawn`, it continues its execution calculating the `amountRemaining`, then transferring the tokens to the recipient.

A user might call this function mistakenly twice in a short period of time which will lead to `allowance = usrClaim.amountWithdrawn` and `amountRemaining = 0`.

The `require` won’t be triggered and the function will continue its execution adding and removing the number 0 from storage variables and calling the `safeTransfer` function with 0 as a parameter.

These extra opcodes will consume more gas from the user which could be avoided.

### Mitigation

Don’t continue the `withdraw` function execution in case the `allowance = usrClaim.amountWithdrawn` to save gas for users.

Change the following line of code:

```solidity
require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
```

To:

```solidity
require(allowance >= usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
```