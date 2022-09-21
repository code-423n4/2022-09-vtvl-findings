### [L-01] Avoid floating pragmas for non-library contracts.


#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
contracts/token/VariableSupplyERC20Token.sol:L2     pragma solidity ^0.8.14;
```
### [N-01] Open TODOs


#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.


#### Findings:
```
contracts/VTVLVesting.sol:L266        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?

```
