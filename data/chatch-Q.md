## Low severity: Potential Out-of-Gas exception

See the for loop in `createClaimsBatch`: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

Iterating over the input arrays to create claims may run out of gas due to the storage requirements of each claim creation.

Consider limiting the number of recipient claims that can be added in one batch.

## Non-critical: Misleading comment

See the comment on the following line: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256

```// Actually only one of linearvested/cliff amount must be 0, not necessarily both```

This is misleading as both amounts can be NON-zero.

The comment ought to say:

```// Actually only one of linearvested/cliff amount must be greater than 0, not necessarily both```

## Non-critical: Openzeppelin contracts version not locked in package.json

The openzeppelin contracts are specified in package.json as:
```json
    "@openzeppelin/contracts": "^4.5.0",
```

but locked in yarn.lock at 4.7.3:
```json
"@openzeppelin/contracts@^4.5.0":
  version "4.7.3"
  resolved "https://registry.yarnpkg.com/@openzeppelin/contracts/-/contracts-4.7.3.tgz#939534757a81f8d69cc854c7692805684ff3111e"
  integrity sha512-dGRS0agJzu8ybo44pCIf3xBaPQN/65AIXNgK8+4gzKd5kbvlqyxryUYVLJv7fK98Seyd2hDZzVEHSWAh0Bt1Yw==
```

So ultimately users of the repo who run `yarn` to install dependencies will get version 4.7.3 of the contracts.

But I think it would be a good idea to change package.json to use:
```
    "@openzeppelin/contracts": "4.7.3",
```

as it will make the version used visible and more explicit to readers of the project code. It will also guarantee the dependency is locked at that version even if a user uses npm to install the dependencies.

## Non-critical: Test structure lost in unit test output for VTVLVesting.ts

Only 4 of the 9 top level `describe` sections headers are rendered in the test output:

```
  ✓ fails on recipientAddress = 0
  ✓ fails on startTimestamp = 0
  ✓ fails on endTimestamp less than startTimestamp
  ✓ fails on invalid interval length
  ✓ fails on vesting duration not a multiple of release interval
  ✓ fails on existing claim
  ✓ fails on invalid cliff 1675209600-0
  ✓ fails on invalid cliff 0-10000000000000000000
  ✓ fails on nothing vested
  ✓ fails on insufficient balance on initial allocation
  ✓ fails on insufficient balance after multiple allocations
  ✓ allocates correct amount (linear: 0, cliff: 1)
  ✓ allocates correct amount (linear: 100000000000000000000, cliff: 0)
  ✓ allocates correct amount (linear: 0, cliff: 10000000000000000000)
  ✓ allocates correct amount (linear: 100000000000000000000, cliff: 10000000000000000000)
  ✓ sets the claim
  ✓ sets the vesting recipient
  ✓ emits ClaimCreated
  ✓ calculates the vested amount before the cliff time to be 0
  ✓ calculates the vested amount at the cliff time to be equal cliff amount
  ✓ correctly calculates the vested amount after the cliff time, but before the linear start time
  ✓ vests correctly if cliff and linear vesting begin are at the same time
  ✓ correctly calculates the vested amount at the linear start time
  ✓ correctly calculates the vested amount after the start
  ✓ correctly calculates the vested amount at 10 of linear interval
  ✓ correctly calculates the vested amount at 25 of linear interval
  ✓ correctly calculates the vested amount at 45 of linear interval
  ✓ correctly calculates the vested amount at 50 of linear interval
  ✓ correctly calculates the vested amount at 70 of linear interval
  ✓ correctly calculates the vested amount at 80 of linear interval
  ✓ correctly calculates the vested amount at 95 of linear interval
  ✓ calculates the vested amount at the end of the linear interval to be the full amount allocated
  ✓ doesn't vest further after the end of the linear interval
  ✓ calculates the finalVestedAmount to be equal the total amount to be vested
  ✓ takes the release interval into account
  ✓ delegates the call to createClaim properly
  ✓ fails when called with invalid param length
  ✓ allows withdrawal up to the allowance and fails after the allowance is spent
  ✓ disallows withdrawal for an user without a claim
  ✓ disallows withdrawal for an user with consumed claim
  ✓ disallows withdrawal for an user with revoked claim
  ✓ calculates the claimable amount to be equal to the vested amount if we have no withdrawals
  ✓ takes withdrawals into account when calculating the claimable amount
  ✓ allows admin to revoke a valid claim
  ✓ prohibits a random user from revoking a valid claim
  ✓ fails to revoke an invalid claim
  ✓ fails to revoke an already withdrawn claim
  ✓ takes revocation into account while calculating the vested amount
  Contract creation
    ✓ can be created with a ERC20 token address
    ✓ fails if initialized without a valid ERC20 token address
    ✓ the deployer is the owner
    ✓ not everyone is admin
    ✓ allows the owner to set and unset other user as an admin
    ✓ fails if attempting to set wrong address as an admin

  Admin withdrawal
    ✓ allows the admin to withdraw the allocated amount
    ✓ reverts on attempt to withdraw more than available
    ✓ doesn't allow random user to admin withdraw

  Other token admin withdrawal
    ✓ allows the admin to withdraw the other token, if present
    ✓ fails on other token withdraw, if token with zero balance
    ✓ fails if called with the main token
    ✓ disallows a non-admin user to withdraw the other token

  Token
    ✓ fails on 0 supply_
    ✓ assigns the correct amount to the creator 
```

This is because of the use of asynchronous functions in describe calls and in particular asynchronous calls at the top of describe function call. For example:
```javascript
describe("Claim creation", async function () {
  const recipientAddress = await randomAddress();
```
at https://github.com/code-423n4/2022-09-vtvl/blob/main/test/VTVLVesting.ts#L183

This can be resolved by removing the unsupported `async` from the function passed to describe AND by moving any asynchronous statements into a `before(async () => { ... })` call. For example the above could be changed to:
```javascript
describe("Claim creation", function () {
  let recipientAddress
  before(async () => {
    recipientAddress = await randomAddress()
  })
```

The structure for that section would then render correctly.
