## Use memory instead of storage keywords when accessing state variable in view functions and in modifiers

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L106

```
    modifier hasActiveClaim(address _recipient) {
        Claim storage _claim = claims[_recipient];
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L123

```
     modifier hasNoClaim(address _recipient) {
        Claim storage _claim = claims[_recipient];
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L197

```
     function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
        Claim storage _claim = claims[_recipient];
        return _baseVestedAmount(_claim, _referenceTs);
    }
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L207

```
     function claimableAmount(address _recipient) external view returns (uint112) {
        Claim storage _claim = claims[_recipient];
        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
    }
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L216

```
     function claimableAmount(address _recipient) external view returns (uint112) {
        Claim storage _claim = claims[_recipient];
        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
    }
```

## Use updated openzeppelin version

the current project openzeppelin version is 

```
    "@openzeppelin/contracts": "^4.5.0",
```

which subject to the vulnerability below

https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts

we recommand the project use the latest openzepplein version

## avoid multilevel inheritance for Context smart contract

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L11

```
 contract VTVLVesting is Context, AccessProtected {
```

but AccessProtected already inherit Context.

We recommand write

```
 contract VTVLVesting is AccessProtected {
```
