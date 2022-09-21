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

## admin can revoke the access of the admin itself and leaving the project with no admin

while the admin address is set to msg.sender of the smart contact AccessProtected

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L17

```
    constructor() {
        _admins[_msgSender()] = true;
        emit AdminAccessSet(_msgSender(), true);
    }
```

the admin can set the admin address itself to false.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39

```
    function setAdmin(address admin, bool isEnabled) public onlyAdmin {
        require(admin != address(0), "INVALID_ADDRESS");
        _admins[admin] = isEnabled;
        emit AdminAccessSet(admin, isEnabled);
    }
```

the admin have access to a lot of high privileged function to keep the project functioning.

So leaving the project with no admin can have bad or unintended effects.

we recommand not let admin revoke the access itself

by adding 

```
 require(admin != msg.sender, "invalid address")
```

code

```
    function setAdmin(address admin, bool isEnabled) public onlyAdmin {
        require(admin != msg.sender, "invalid address");
        require(admin != address(0), "INVALID_ADDRESS");
        _admins[admin] = isEnabled;
        emit AdminAccessSet(admin, isEnabled);
    }
```





