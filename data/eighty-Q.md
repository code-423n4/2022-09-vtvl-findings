## Unused imports

To improve readability and avoid confusion, consider removing the following unused imports:

Instances:

*   [VTVLVesting.sol#L6](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L6): `import "@openzeppelin/contracts/access/Ownable.sol"`
*   [AccessProtected.sol#L5](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L5): `import "@openzeppelin/contracts/access/Ownable.sol"`


### Recommendation

Consider removing the unused import if it is not required.

## Unnecessary use of Context

No use case has been identified that justifies the use of OpenZeppelin's Context. Its use may only be justified when dealing with meta transactions (e.g. [EIP-2771](https://eips.ethereum.org/EIPS/eip-2771))

### Recommendation

Consider simplifying the code base and use `msg.sender`.
