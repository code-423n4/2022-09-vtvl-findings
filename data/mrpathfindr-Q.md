
VTVLVesting.sol#147-188  performs a multiplication on the result of a division
---


Proof of Concept: 

if ` _claim.releaseIntervalSecs` is ever greater than `currentVestingDurationSecs` then `truncatedCurrentVestingDurationSecs` could be 0

Reference: 

https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply



Instances Include: 

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L169

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L176


Mitigation:

Consider ordering multiplication before division.


```
  uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs * _claim.releaseIntervalSecs) / _claim.releaseIntervalSecs
```










