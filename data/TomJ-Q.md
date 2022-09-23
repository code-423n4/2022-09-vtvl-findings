## Table of Contents
### Low Risk Issues
- Same address can't be assigned as Vesting Recipients even though vesting schedule is finished
- Duplicate `Context` inheritance
### Non-critical Issues
- Use fixed compiler versions instead of floating version
- Event is Missing Indexed Fields

&ensp;
## Low Risk Issues
### Same address can't be assigned as Vesting Recipients even though vesting schedule is finished

#### Issue
There might be an occasion where administrators (founders) wants to deploy the token vesting with same token again.
In currenct VTVLVesting.sol contract, it is not possible to assign same address as vesting recipients even though the 
vesting schedule is finished (and all of its vest amount is withdrawn) because `claim.isActive` is always true 
once a claim is created (It will only be false again if admin execute `revokeClaim()` before all of its vest amount is withdrawn). 
Therefore it might be ideal to set `claim.isActive` to false once all of the vest amount is withdrawn.

#### PoC
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L364-L392

#### Mitigation
Add below code after line 384 to make claim not active after all its vest amount is withdrawn
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L364-L392
```
uint112 finalVestAmt = finalVestedAmount(_msgSender());
if ( usrClaim.amountWithdrawn == finalVestAmt ) {
    usrClaim.isActive = false; 
}
```

&ensp;
### Duplicate `Context` inheritance

#### Issue
`Context` inheritance is unnecessary for `VTVLVesting.sol` since it is already inherited at `AccessProtected.sol`.

#### PoC
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L11
```
VTVLVesting.sol:
contract VTVLVesting is Context, AccessProtected {
```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L11
```
AccessProtected.sol:
abstract contract AccessProtected is Context {
```

#### Mitigation
Remove `Context` from `VTVLVesting.sol` like shown in below example.
```
Example:
contract VTVLVesting is AccessProtected {
```

&ensp;
## Non-critical Issues
### Use fixed compiler versions instead of floating version

#### Issue
it is best practice to lock your pragma instead of using floating pragma.
the use of floating pragma has a risk of accidentally get deployed using latest complier
which may have higher risk of undiscovered bugs.
Reference: https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/

#### PoC
```
./VariableSupplyERC20Token.sol:2:pragma solidity ^0.8.14;
```

#### Mitigation
I suggest to lock your pragma and aviod using floating pragma.
```
// bad
pragma solidity ^0.8.10;

// good
pragma solidity 0.8.10;
```

&ensp;
### Event is Missing Indexed Fields

#### Issue
Each event should have 3 indexed fields if there are 3 or more fields.

#### PoC
```
./VTVLVesting.sol:59:    event ClaimCreated(address indexed _recipient, Claim _claim); 
./VTVLVesting.sol:64:    event Claimed(address indexed _recipient, uint112 _withdrawalAmount); 
./VTVLVesting.sol:69:    event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
./VTVLVesting.sol:74:    event AdminWithdrawn(address indexed _recipient, uint112 _amountRequested);
./AccessProtected.sol:14:    event AdminAccessSet(address indexed _admin, bool _enabled);
```

#### Mitigation
Add up to 3 indexed fields when possible.

&ensp;