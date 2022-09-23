
## Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

### Instances:

```
VTVLVesting.sol:266:        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
``` 
### References:

[https://code4rena.com/reports/2022-05-sturdy/#l-09-open-todos](https://code4rena.com/reports/2022-05-sturdy/#l-09-open-todos)


-----

## Avoid using Floating Pragma:

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Instances:

```
token/VariableSupplyERC20Token.sol:2:pragma solidity ^0.8.14;
``` 
Recommend using fixed solidity version

### References:

[https://code4rena.com/reports/2022-04-phuture#g-20-use-a-more-recent-version-of-solidity](https://code4rena.com/reports/2022-04-phuture#g-20-use-a-more-recent-version-of-solidity)


-----

## Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Instances:
```
VTVLVesting.sol:217:        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
VTVLVesting.sol:371:        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
VTVLVesting.sol:436:        emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);
``` 
### References:

https://github.com/code-423n4/2022-04-dualityfocus-findings/issues/33


-----
## _safeMint() should be used rather than _mint() wherever possible

### Instances
```
token/VariableSupplyERC20Token.sol:45:        _mint(account, amount);
token/FullPremintERC20Token.sol:12:        _mint(_msgSender(), supply_);
``` 
### Recommendations:
Use _safeMint() instead of _mint().