1.	Use `external` modifier instead of `public` if the method is not called by contract.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398
2.	Use `require(_claim.isActive, "NO_ACTIVE_CLAIM")` instead
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L111
3.	All `todo` should be already closed when you finished development.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L266