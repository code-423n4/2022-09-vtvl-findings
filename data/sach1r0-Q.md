# `withdrawOtherToken()` is an admin-only function but does not emit an event

## Details                                                                 
Admin-only functions that change critical parameters should emit events. Events allow capturing the changed parameters so that offchain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.                            
## Mitigation
I suggest emitting an event like the other admin-only function does.

## Line of Code
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L446-L451

___
# Open TODO

##  Open TODO is present in [VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol)

## Mitigation
I suggest avoiding open TODOs as they may indicate errors that still needs to be fixed

## Line of code
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266


____
# Remove commented out code

## Line of code
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L261