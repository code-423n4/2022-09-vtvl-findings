# Summary
## Low
[L01]. A floating pragma is set
[L02]. Outdated compiler version

## Non Critical
[NC01]. Event is missing indexed fields
[NC02]. Public functions that are not being used inside de contract should be external
[NC03]. Code in comments
[NC04]. TODO comments means it's not a final version
[NC05]. File is missing NatSpec

# Low
## [L01] A floating pragma is set
### Description
The contract VariableSupplyERC20Token have the pragma solidity directive ^0.8.14. It is recommended to specify a fixed compiler version to ensure that the bytecode produced 
does not vary between builds. 
This is especially important if you rely on bytecode-level verification of the code.

### Mitigation
Lock the pragma.

### Lines in the code
[VariableSupplyERC20Token.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L2)

## [L02] Outdated compiler version
It's a best practice to use the latest compiler version.
The specified minimum compiler version is quite old (0.8.14). Older compilers might be susceptible to some bugs. 
It's recommended changing the solidity version pragma to the latest version to enforce the use of an up-to-date compiler.

A list of known compiler bugs and their severity can be found here: https://etherscan.io/solcbuginfo

To check the bugfixed and improvements of latest versions see the following [link](https://github.com/ethereum/solidity/releases)

### Mitigation
Update the pragma to 0.8.16

### Lines in the code
[FullPremintERC20Token.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L2)
[VariableSupplyERC20Token.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L2)
[AccessProtected.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L2)
[VTVLVesting.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L2)

# Non Critical
## [NC01] Event is missing indexed fields
### Description
Index event fields make the field more quickly accessible to off-chain tools that parse events. 
However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). 
Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. 
If there are fewer than three fields, all of the fields should be indexed.

### Lines in the code
[AccessProtected.sol#L14](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L14)
[VTVLVesting.sol#L59](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L59)
[VTVLVesting.sol#L64](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L64)
[VTVLVesting.sol#L69](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L69)
[VTVLVesting.sol#L74](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L74)

## [NC02] Public functions that are not being used inside de contract should be external

### Lines in the code
[AccessProtected.sol#L39](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39)
[VTVLVesting.sol#L398](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398)

## [NC03] Code in comments
### Description
There are many places in the code with code commented. It's a good practice not to leave code in comments.
Clean unnecesary comments.

### Lines in the code
[VTVLVesting.sol#L114-L115](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L114-L115)
[VTVLVesting.sol#L133](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L133)
[VTVLVesting.sol#L136-L138](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L136-L138)
[VTVLVesting.sol#L261](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L261)

## [NC04] TODO comments means it's not a final version
### Description
There is a TODO comment inside the code what can mean that the code has not been finalished. 
Make sure that the code is finished before deployment in production.

### Lines in the code
[VTVLVesting.sol#L266](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266)

## [NC05] File is missing NatSpec
[FullPremintERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol)
[VariableSupplyERC20Token.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol)
[AccessProtected.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol)
[VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol)
