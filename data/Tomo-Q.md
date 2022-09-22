# QA Report

## ✅ N-1: Non-library/interface files should use fixed compiler versions, not floating ones

### 📝 Description
Non-library/interface files should use fixed compiler versions, not floating ones

### 💡 Recommendation
Delete the floating keyword `^`.

### 🔍 Findings:
```2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2``` [pragma solidity ^0.8.14;](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2 )


## ✅ N-2: Open Todos

### 📝 Description
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

### 💡 Recommendation
Delete TODO keyword

### 🔍 Findings:
```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L266``` [// Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L266 )


## ✅ N-3: No use of two-phase ownership transfers

### 📝 Description
Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call `accept*()` to become the new owner. This prevents passing the ownership to an account that is unable to use it.

### 💡 Recommendation
Consider implementing a two step process where the admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of admin to fully succeed. This ensures the nominated EOA account is a valid and active account.

### 🔍 Findings:
```2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L5``` [import "@openzeppelin/contracts/access/Ownable.sol";](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L5 )

```2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L6``` [import "@openzeppelin/contracts/access/Ownable.sol";](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L6 )
