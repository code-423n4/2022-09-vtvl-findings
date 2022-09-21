## Confusing and misleading variable name in VariableSupplyERC20Token.sol

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L19

```
     @param maxSupply_ - What's the maximum supply. The contract won't allow minting over this amount. Set to 0 for no limit.
```

the variable maxSupply is used as the supply amount that can be minted, not used to restrict the total supply,

we recommand the project change the name from maxSupply to mintable supply.


## Lack of sufficient testing for FullPermintERC20Token.sol and in VariableSupplyERC20Token.sol

the test use TestERC20Token.sol to conduct the test,

but integration test and unit test for FullPermintERC20Token.sol and in VariableSupplyERC20Token.sol is needed.

for example, in the code, 

the initial supply is 1000

because in the TestERC20Token.sol, the supply is multiple by decimals.

```
    uint256 supplyToMint = initialSupply_ * (10 ** decimals());
```

but in FullPermintERC20Token.sol and in VariableSupplyERC20Token.sol

there is no such multiplication. 

So please make sure we multiple by decimals before passing 
into the constructor of FullPermintERC20Token.sol and in VariableSupplyERC20Token.sol

## USE OF FLOATING PRAGMA

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

https://swcregistry.io/docs/SWC-103

in VariableSupplyERC20Token.sol

```
 pragma solidity ^0.8.14;
```

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