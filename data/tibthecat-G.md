# Require unnecessary in **hasActiveClaim** function

## Problem:
This require is unnecessary: **require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");**

Because the other require: **require(_claim.isActive == true, "NO_ACTIVE_CLAIM");** is sufficient

## Suggestion:

Remove this require:
**require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");**



# function **finalVestedAmount** could be optimized 


## Problem:
This function uses same computation than **vestedAmount** function with endTimestamp as parameter
but we don't need to do all the maths as we know the result will be: **linearVestAmount+cliffAmount**
 
## Suggestion:

Optimize the function (keep the requires but optimize the calculation to save gas)




# function **_baseVestedAmount** could be optimized when **linearVestAmount == 0**


## Problem:
Even if this parameter is 0, linearVestAmount is computed which costs gas and is unnecessary
 
## Suggestion:
Add a condition:
            **if(_referenceTs > _claim.startTimestamp && _claim.linearVestAmount)**
so that the maths is skipped.
             

# Replace some **memory** by **calldata**


## Problem:
Some memory parameters could be replaced by calldata to save gas
 
## Suggestion:
For example in **createClaimsBatch** if some of the array parameters are **calldata** instead of **memory**, it saves gas
Note: it looks like compiler does not allow to use calldata on all the inputs of this function, but it allows to do it on 3 of them which already seems to save some gas.

# Some **Unchecked** could be added 


## Problem:
With Solidity 8, overflows/underflow are automatically handled. But as we control how and when additions or substractions are done, we can be sure that there won't be any overflow.
 
## Suggestion:
Use **Unchecked** keyword in some parts of the code to save gas (in **_baseVestedAmount**, in the claimable amount computation for example)




