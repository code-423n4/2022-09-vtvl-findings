## Admin might remove himself
Code: https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39-L43

There's no check that the admin isn't `_msgSender()`, in a scenario where the sender is only admin that can lead to loss of governance over the protocol.

### Impact
Losing governance over the protocol will lead to loss of the ability to:
* Create new claims
* Revoke claims
* Withdrawing any remaining funds (`withdrawAdmin()`)

Meaning, any remaining funds that aren't reserved for existing claims will be frozen.

### Mitigation
Require that the `_msgSender() != admin`



## Zero initial supply allowed when max supply is limited but not when it's unlimited
Code: https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27

There's already a comment in the code addressing this, but I'd just like to point out that there's no reason to require initial supply to be greater than zero only when the max supply is unlimited.
Not allowing initial supply to be zero when more tokens can be minted afterwards (unlimited > limited) doesn't make much sense.
### Mitigation
Remove the requirement at line 27 (`require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");`).
In anyways the *actual* max supply is greater than zero.