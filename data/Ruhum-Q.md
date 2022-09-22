# Report

- [Low](#low)
  - [L-01: hot wallet has admin rights](#l-01-hot-wallet-has-admin-rights)
  - [L-02: documentation about security of funds is misleading](#l-02-documentation-about-security-of-funds-is-misleading)

# Low

## L-01: hot wallet has admin rights

It's bad practice to grant a hot wallet admin rights. These are generally less secure than a multisig or timelock contracts. There's a real risk of these keys being compromised. They generally tend to sit on a machine of one of the devs because they are used for deployment.

- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L17

You should make it very clear that the admin rights should NOT be given to the hot wallet or at least removed immediately after the deployment of the contracts. Instead, they should use a multisig.

## L-02: documentation about security of funds is misleading

Inside the contest's readme, you state that:
> No one other than the designated vesting recipient (not even the admin) can withdraw on an existing claim - but the admin can revoke the claim

https://github.com/code-423n4/2022-09-vtvl#vesting-recipients

This makes it sound like that the admin can only revoke your access to the tokens. Effectively, those tokens would then be locked up in the contract. But, they can withdraw them after revoking the user's access. So the admin *is* able to withdraw the tokens from an existing claim.

I understand the reason behind the functionality. I just believe that that part should be reworded in the official documentation.