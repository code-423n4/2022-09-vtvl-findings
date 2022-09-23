# Using unnecessary storage

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L197
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L207
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L216

```
    function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
        Claim storage _claim = claims[_recipient];
        return _baseVestedAmount(_claim, _referenceTs);
    }
```

_claim are not modified so change to memory to save gas.

## recommendation
```
    function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
        Claim memory_claim = claims[_recipient];
        return _baseVestedAmount(_claim, _referenceTs);
    }