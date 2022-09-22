




G012 - Use Prefix Increment instead of Postfix Increment if possible|
---


Instances:

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353


Mitigation:

Do this:

```
 for (uint256 i = 0; i < length; ++i) {
```

Instead of this:

```
 for (uint256 i = 0; i < length; i++) {
```


