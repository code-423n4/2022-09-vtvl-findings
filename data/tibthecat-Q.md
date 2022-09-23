
# In **AccessProtected.sol**, **setAdmin** should be external


## Problem:
The function could be external as it's never called from the contract

 
## Suggestion:
Change the visibility to external



# Some comments are incorrect


## Problem:

### // Every how many seconds does the vested amount increase. 
Should be **unvested** or **decrease** because the vestedAmount does not increase with the time

### // We don't check here that cliffReleaseTimestamp is after the startTimestamp 
Should be **before** as cliffReleaseTimestamp  is always before startTimestamp 

###    require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
ok tokenAddress is nonzero, but here we only check that _otherTokenAddress is different from **tokenAddress** so this comment is pointless

## Suggestion:
Fix the comments or remove them as they could be misleading for someone reading the code.
             