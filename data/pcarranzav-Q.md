# Summary

I've reviewed all of the Solidity code in scope, and found what I believe are two Medium severity issues that I've reported
separately. Other than that, I think the code looks clean, and I appreciate the minimal design for the vesting contract. Here I'll just present some non-critical suggestions that I think could help improve the code's readability and maintainability.

# Non-critical findings

## Using uint112 for amounts

Is there a strong reason for using the unusual type uint112? If it's only for gas, I believe using the native type for the EVM could be safer
and more convenient in the long run.

Keep in mind different tokens might want to use various values for the token decimals, so having the largest possible
number type could give founders more flexibility when designing the token.

Changing everything to uint256 does produce a 10-20% gas increase in some functions, so I understand if you prefer to keep it this way. Even using uint128 could make things a bit safer and more flexible, and this barely introduces a gas increase compared to uint112. In any case I'd recommend documenting the reason for this decision so that it's clearer when looking at the code.

## Name of the Claimed event

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L64

With the word Claim being used for the main struct, I would suggest a more specific name,
like ClaimTokensWithdrawn.

## Use of the same revert message

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L107-L111

I'd recommend using a different revert message for each case, to make debugging easier for users, e.g.

```solidity
        Claim storage _claim = claims[_recipient];
        require(_claim.startTimestamp > 0, "NO_CLAIM");

        // We however still need the active check, since (due to the name of the function)
        // we want to only allow active claims
        require(_claim.isActive == true, "CLAIM_NOT_ACTIVE");
```

## Non-explicit imports

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L5-L9

I recommend changing these to `import { Foo } from ".../Foo.sol"` to import exactly what's needed from each file. This helps avoid accidental name collisions.

## Complex require logic

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L270-L278

This is a bit hard to read. It could be unfolded to something more explicit like:

```solidity
if (_cliffReleaseTimestamp > 0) {
    require(_cliffAmount > 0 && _cliffReleaseTimestamp <= _startTimestamp, "INVALID_CLIFF");
} else {
    require(_cliffAmount == 0, "INVALID_CLIFF");
}
```

Or even more verbose:

```solidity
if (_cliffReleaseTimestamp > 0) {
    require(_cliffAmount > 0, "INVALID_CLIFF_AMOUNT_ZERO");
    require(_cliffReleaseTimestamp <= _startTimestamp, "INVALID_CLIFF_RELEASE_GT_START");
} else {
    require(_cliffAmount == 0, "INVALID_CLIFF_AMOUNT_NONZERO");
}
```

## Unused Ownable import

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L5

The AccessProtected contract is not Ownable. Did you mean to add an owner role? If not, I'd suggest removing the unused
import to prevent confusion.

## Inaccurate reason for not allowing burning

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L48-L49

The VariableSupplyERC20Token comments specify that being able to burn tokens could break the vesting contracts.
If only the owner of certain tokens can burn them, there is no way to burn any tokens on the vesting contract without first withdrawing them. Therefore, I think it'd be safe to make the token burnable.

## Setting the first admin to msg.sender

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L16-L19

I think this sets some unnecessary restrictions to how this contract can be deployed - for composability, it might be desirable to e.g. deploy this contract from another contract, in which case setting the admin to msg.sender will make the contract inoperable. Or a user might want to use a deployer account that is separate from the account used to administer the funds,
in which case two more transactions are needed to add the new admin and remove the deployer.

Making this more flexible only adds a bit more gas on the deployment, so I'd recommend changing the constructor to allow specifying a separate address for the first admin:

```solidity
    constructor(address initialAdmin) {
        require(initialAdmin != address(0), "!ADMIN");
        _admins[initialAdmin] = true;
        emit AdminAccessSet(initialAdmin, true);
    }
```

(You'd then need to modify any contracts that inherit this to call the constructor accordingly)

Or if you still want to default to msg.sender, you can allow passing a zero address to initialAdmin:

```solidity
    constructor(address initialAdmin) {
        if (initialAdmin == address(0)) {
            initialAdmin = _msgSender();
        }
        _admins[initialAdmin] = true;
        emit AdminAccessSet(initialAdmin, true);
    }
```
