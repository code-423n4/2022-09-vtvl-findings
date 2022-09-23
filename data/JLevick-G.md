## Gas Report

1. Comment states finalVestedAmount() is superfluous, deployment gas can be saved by removing it and updating line 422 to:
```
uint112 finalVestAmt = vestedAmount(_recipient, _claim.endTimestamp);
```
[VTVLVesting.sol#L201-L209](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L201-L209)
[VTVLVesting.sol#L422](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L422) 
| VTVLVesting | Pre Changes | Post Changes | Savings |
| ---------- | ----------- | ----------- | ----------- |
| Deployment | 3,740,739 | 3,621,851 | 118,888 |

2. It is unneccessary to assign state variables to their default value

[VTVLVesting.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27) - can update to: 
```
uint112 public numTokensReservedForVesting;
```
| VTVLVesting | Pre Changes | Post Changes | Savings |
| ---------- | ----------- | ----------- | ----------- |
| Deployment | 3,740,739 | 3,737,632 | 3,107 |

3. Using x = x +/- y is cheaper than x +/-= y for state variables

[VTVLVesting.sol#L383](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L383)
[VTVLVesting.sol#L433](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L433)
[VTVLVesting.sol#L301](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L301)
[VTVLVesting.sol#L381](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L381) 

| VTVLVesting | Pre Changes | Post Changes | Savings |
| ---------- | ----------- | ----------- | ----------- |
| Deployment | 3,740,739 | 3,740,295 | 444 |
| createClaim | 167,757 | 167,752 | 5 |
| createClaimsBatch | 284,486 | 284,462 | 24 |
| withdraw | 72,938 | 72,928 | 10 |

4. Custom errors are cheaper than require statements.

| VTVLVesting | Pre Changes | Post Changes | Savings |
| ------------ | ------------- | ------------- | ------------- |
| Deployment | 3,740,739 | 3,738,771 | 1,968 |

Gas savings above are from updating 1 require statement to a custom error, there are 24 require statements throughout the VTVL contracts, so updating them all will give gas savings of ~48,000.
[FullPremintERC20Token.sol#L11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11) 
[AccessProtected.sol#L25](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L25) 
[AccessProtected.sol#L40](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L40)
[VariableSupplyERC20Token.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27) 
[VariableSupplyERC20Token.sol#L37](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L37) 
[VariableSupplyERC20Token.sol#L41](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41) 
[VTVLVesting.sol#L82](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L82) 
[VTVLVesting.sol#L107](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107) 
[VTVLVesting.sol#L111](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111) 
[VTVLVesting.sol#L129](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L129) 
[VTVLVesting.sol#L255-L257](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255-L257)
[VTVLVesting.sol#L262-L264](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L262-L264) 
[VTVLVesting.sol#L270-L278](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270-L278) 
[VTVLVesting.sol#L295](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L295) 
[VTVLVesting.sol#L344-L351](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L351) 
[VTVLVesting.sol#L374](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374) 
[VTVLVesting.sol#L402](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L402) 
[VTVLVesting.sol#L426](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L426) 
[VTVLVesting.sol#L447-L449](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L447-L449) 