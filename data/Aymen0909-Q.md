# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1       |  `numTokensReservedForVesting` could overflow for certain ERC20 tokens | Low | 1 |
| 2      | Openzeppelin `Ownable` extension imported but not used  | NC | 2 |
| 3      | Redundant ZERO address check inside the `mint` function | NC | 1  |
| 4      | Non-library/interface files should use fixed compiler versions, not floating ones | NC| 1  |

## Findings

### 1- `numTokensReservedForVesting` could overflow for certain ERC20 tokens :

The `numTokensReservedForVesting` state variable uses `uint112` which has a maximum value of `2**112 = 5.1922969e+33`, this value could be not sufficient as both `cliffAmount` and  `linearVestAmount` also uses `uint112`, so if both those variable get a big value their sum could be greater that 2**112 which will lead to the vesting contract to be unable of creating new claims.

This issue could occur for example if we take an ERC20 token with 18 decimals and doesn't cost a lot in term of USD let say `1 token = 0.000000001 USD` (which is possible with new ERC20 tokens) so the values of both `cliffAmount` and  `linearVestAmount` variable could be very close to the uint112 limit and thus their sum would be greater than 2**112 .

#### Impact - Low

#### Proof of Concept
Instances include:

File: contracts/VTVLVesting.sol [Line 292-301](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L292-L301)

```
uint112 allocatedAmount = _cliffAmount + _linearVestAmount;

numTokensReservedForVesting += allocatedAmount;
```

#### Mitigation
Consider using `uint256` for the variable `numTokensReservedForVesting` to avoid any overflow risks, or make sure to pick ERC20 tokens which doesn't have very low prices.

### 2- Openzeppelin `Ownable` extension imported but not used :

The Openzeppelin `Ownable` extension is imported in both the `VTVLVesting` and `AccessProtected` contracts but none of them has inherited it, maybe the import statement was just forgotten or left for later use if not it should be removed.

Note that the `AccessProtected` contract is itself acting as an access control contract, so if `Ownable` extension is set to be used in the future devs must make sure to correctely manage both those access control extensions.

#### Impact - NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/VTVLVesting.sol

[import "@openzeppelin/contracts/access/Ownable.sol";](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L6)

File: contracts/AccessProtected.sol

[import "@openzeppelin/contracts/access/Ownable.sol";](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L5)

#### Mitigation
Remove the Openzeppelin `Ownable` import statement if it's not intended to be used.

### 3- Redundant ZERO address check inside the `mint` function :

The `_mint` function from the `Openzeppelin ERC20` contract already contains a check to verify that the reciever account is not the zero address, so it's redundant to add another check in the `mint` function as it will call the `_mint` function which will revert if `account == address(0)`.

[_mint function from the Openzeppelin ERC20 contract](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol#L259-L272)

```
function _mint(address account, uint256 amount) internal virtual {
    require(account != address(0), "ERC20: mint to the zero address");

    _beforeTokenTransfer(address(0), account, amount);

    _totalSupply += amount;
    unchecked {
        // Overflow not possible: balance + amount is at most totalSupply + amount, which is checked above.
        _balances[account] += amount;
    }
    emit Transfer(address(0), account, amount);

    _afterTokenTransfer(address(0), account, amount);
}
```

#### Impact - NON CRITICAL

#### Proof of Concept

Instances occurs in :

File: contracts/token/VariableSupplyERC20Token.sol

[require(account != address(0), "INVALID_ADDRESS");](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L37)

#### Mitigation

Remove the zero address check aforementioned as it's redundant.

### 4- Non-library/interface files should use fixed compiler versions, not floating ones :

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

check this source : https://swcregistry.io/docs/SWC-103

#### Impact - NON CRITICAL

#### Proof of Concept
The `VariableSupplyERC20Token` contract uses a floating solidity version in contrary to others contracts: 

```
pragma solidity ^0.8.14;
```
#### Mitigation
Lock the pragma version to the same version as used in the other contracts and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile it locally.
