# QA

# Low Severity

## [L-01] Typos in comments and wrong comments

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

### Findings :

1.

```solidity
token/VariableSupplyERC20Token.sol:28:        // max supply == 0 means mint at will.
```

Instead of :

```solidity
token/VariableSupplyERC20Token.sol:28:        // maxSupply_ == 0 means mint at will.
```

2.

```solidity
token/VariableSupplyERC20Token.sol:49:        // mintableSupply = 0 means mint at will
```

Instead of :

```solidity
token/VariableSupplyERC20Token.sol:49:        // mintableSupply == 0 means mint at will
```

3.

```solidity
2022-09-vtvl/contracts/VTVLVesting.sol:373:        // Make sure we didn't already withdraw more that we're allowed.
```

Instead of :

```solidity
2022-09-vtvl/contracts/VTVLVesting.sol:373:        // Make sure we don't withdraw more that we're allowed.
```

4.

```solidity
token/VariableSupplyERC20Token.sol:31:        // However, if both are 0 we might have a valid scenario as well - user just wants to create a token but doesn't want to mint anything
```

- if both are 0 : `maxSupply_ == 0` and `initialSupply_ == 0` => mint at will and nothing preminted, which is fine.

Which means the comment above 4. is wrong because they can mint at will when `maxSupply_ == 0`.

5.

If `maxSupply_ == 0` means mint at will as mentionned in the following comment :

```solidity
token/VariableSupplyERC20Token.sol:28:        // max supply == 0 means mint at will.
```

It has for consequence that there is no scenarios in which the tokens are not mintable.

6.

To answer the question in comment :

```solidity
token/VariableSupplyERC20Token.sol:36:        // Should we allow this?
```

All the others scenarios :

- if both are != 0 : `maxSupply_ != 0` and `initialSupply_ != 0` => mint capped by `maxSupply_` and preminted amount, which is fine if `maxSupply_ >= initialSupply_`.

- if `maxSupply_ != 0` and `initialSupply_ == 0` => mint capped by maxSupply_ and no preminted amount, which is fine.

- if `maxSupply_ == 0` and `initialSupply_ != 0` => mint at will and preminted amount, which is fine.



# Non-critical

## [N-01] Floating pragma compiler version

Contracts should not use a floating pragma in order to ensure that the code has been tested and deployed with the same version.

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

### Findings :

```solidity
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::2 	 pragma solidity ^0.8.14;
```

## [N-02] Functions should be declared after modifiers

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

### Findings :

```solidity
2022-09-vtvl/contracts/VTVLVesting.sol:91:    function getClaim(address _recipient) external view returns (Claim memory) {
```

## [N-03] Missing `address(0)` check

An address(0) check could be added in the function `VTVLVesting.withdrawOtherToken` to the input address `_otherTokenAddress`.

### Files links :

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

### Findings :

```solidity
2022-09-vtvl/contracts/VTVLVesting.sol:446:     function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
                                                    require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
                                                    uint256 bal = _otherTokenAddress.balanceOf(address(this));
                                                    require(bal > 0, "INSUFFICIENT_BALANCE");
                                                    _otherTokenAddress.safeTransfer(_msgSender(), bal);
                                                }
```