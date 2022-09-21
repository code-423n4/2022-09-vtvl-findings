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