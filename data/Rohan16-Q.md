# L-01 Remove TODO's

### Description 
TODO's should be removed before the deployment of a contracts where this potential TODO is not a big concern but should be renmoved.Tagging the line [here](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266)

```
contracts/VTVLVesting.sol:266:        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
```

//WRITTEN CODE IN PROGRAM 
```
  require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?

        // No point in allowing cliff TS without the cliff amount or vice versa.
        // Both or neither of _cliffReleaseTimestamp and _cliffAmount must be set. If cliff is set, _cliffReleaseTimestamp must be before or at the _startTimestamp
        require( 
            (
```

### Recommendation

Before the deployment of contracts removal of TODOs make it professional.

# L-02 Remove commented out codes
 Codes should be given in the main line instead of comment.

```
contracts/VTVLVesting.sol:114:        // require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
contracts/VTVLVesting.sol:115:        // require(_claim.endTimestamp > 0, "NO_END_TIMESTAMP");
contracts/VTVLVesting.sol:261:        // require(_endTimestamp > 0, "_endTimestamp must be valid"); // not necessary because of the next condition (transitively)

```
 # L-03 Use of Recent version of solidity
 
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

```

contracts/test/TestERC20Token.sol:2:pragma solidity ^0.8.0;
```

