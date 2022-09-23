### [G-01] `mintableSupply` can be made immutable by looking at the token's total supply.

Variable `mintableSupply` from `VariableSupplyERC20Token.sol` can be made immutable instead of storage.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L11

The `mint` function from said contract can be modified to only mint if the condition `totalSupply() + amount <= mintableSupply` is satisfied.

```solidity=
function mint(address account, uint256 amount) public onlyAdmin {
    require(account != address(0), "INVALID_ADDRESS");
    if (mintableSupply > 0) {
        require(totalSupply() + amount <= mintableSupply, "INVALID_AMOUNT");
    }
    _mint(account, amount);
}
```

The gas cost of the addition operation can be further eliminated by placing the check after the mint function:

```solidity=
function mint(address account, uint256 amount) public onlyAdmin {
    require(account != address(0), "INVALID_ADDRESS");
    _mint(account, amount);
    require(totalSupply() <= mintableSupply, "INVALID_AMOUNT");
}
```

Saves one Gsset (20000 gas) on construction, as well as an SLOAD (2100 slot) read into the original `mintableSupply` slot each time the `mint` function is called.

### [G-02] Using `!= 0` for non-zero uint comparison takes less gas than `>0`

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L257
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449

### [G-03] `_admins`: Using `bool` data type for storage incurs extra gas overhead

```solidity=
mapping(address => bool) private _admins;
```

Use `uint256` for less gas cost

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L12

### [G-04] `numTokensReservedForVesting`: Uses smaller-than-32-bytes data type, as well as initializing default value

Both of which costs extra gas

```solidity=
uint112 public numTokensReservedForVesting = 0;
```

Can be changed to the following

```solidity=
uint256 public numTokensReservedForVesting;
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27

### [G-05] `++i` costs less gas than `i++`

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

### [G-06] `require` statements with `&&` can be split into multiple requires to save gas

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L344


