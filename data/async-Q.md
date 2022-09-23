# Low
## VTVLVesting and AcessProtected import contracts which are not inherited
## Impact
Deployment cost is higher by importing duplicitive code
## Proof of Concept
AccessProtected imports Ownable but does not inherit it.
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L1-L11

VTVLVesting imports Context and Ownable which is unnecessary since they are imported by AccessProtected which VTVLVesting inherits.
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L1-L12
## Tools used
Manual analysis
## Recommended Mitigation Steps
In VTVLVesting, remove the import of OpenZeppelin contracts Context and Ownable since they are already imported by AccessProtected which is an inherited contract.

In AccessProtected, remove the import of Ownable since it is not used.

# Non-critical
# Stale comment
In FullPremintERC20Token, this piece of code is commented out `L09 // uint constant _initialSupply = 100 * (10**18);`. Either remove the stale comment or include the code.