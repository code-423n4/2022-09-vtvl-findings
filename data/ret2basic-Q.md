# VTVL Contest QA Report

## Summary

The following QA issues were found during the code audit:

1. Unspecific Compiler Version Pragma (1 instance)
2. Comparing boolean to `true`/`false` is redundant (1 instance)

Total 2 instances of 2 issues.

## 1. Unspecific Compiler Version Pragma (1 instance)

Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
contracts/token/VariableSupplyERC20Token.sol::2 => pragma solidity ^0.8.14;
```

## 2. Comparing boolean to `true`/`false` is redundant (1 instance)

The following code can be written as `require(_claim.isActive, "NO_ACTIVE_CLAIM")`. Comparing `_claim.isActive` to `true`/`false` is redundant.

```solidity
contracts/VTVLVesting.sol::111 => require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```
