## [L-01] Use of floating pragma

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

<https://swcregistry.io/docs/SWC-103>

### Findings

```solidity
contracts/token/VariableSupplyERC20Token.sol
2: pragma solidity ^0.8.14;
```

### Mitigation
Lock the pragma version to the same version as used in the other contracts and also consider known bugs (<https://github.com/ethereum/solidity/releases>) for the compiler version that is chosen.

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile it locally.


## [N-01] No need to explicitly initialize variables with default values
If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address...). Explicitly initializing it with its default value is an anti-pattern.

As an example: `for (uint256 i = 0; i < numIterations; ++i) {` should be replaced with `for (uint256 i; i < numIterations; ++i) {`

### Findings
```solidity
contracts/VTVLVesting.sol
148:         uint112 vestAmt = 0;

353:         for (uint256 i = 0; i < length; i++) {
```
### Mitigation
Remove explicit initializations for default values.

## [N-02] Event is missing indexed fields
Each `event` should use three `indexed` fields if there are three or more fields
### Findings
```solidity
contracts/VTVLVesting.sol
69:     event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
```

### Mitigation
Add `indexed` to `_numTokensWithheld` and `revocationTimestamp` fields.