## Gas Optimizations
22/09/2022 14:57
[VTVL](https://github.com/code-423n4/2022-09-vtvl)

### Savings

The following changes pass [the given tests](https://github.com/code-423n4/2022-09-vtvl/tree/main/test) saving approximately 47 BPS (optimizer off).


**From**
Methods|Min|Max|Avg|
|-|-|-|-|
|TestERC20Token|-|-|1193264|
|VTVLVesting|3740728|3740740|3740739|

**To**

Methods|Min|Max|Avg|
|-|-|-|-|
|TestERC20Token|-|-|1193264|
|VTVLVesting|3723229|3723241|3723240|

**Tools Used:** Manual Analysis, [the given test suite](https://github.com/code-423n4/2022-09-vtvl/tree/main/test).


### VTVLVesting.sol

**There is no need to initialize variables with default values**

* Line: [27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol?plain=1#L27) 
* Line [148](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol?plain=1#L148) 
* Line [353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol?plain=1#L353)


[Reference](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#3--there-is-no-need-to-initialize-variables-with-default-values)

**Inline variables to save SLOAD**
* Line [124](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol?plain=1#L124)
* Line [197](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol?plain=1#L197) 

Example: 
```
function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
    Claim storage _claim = claims[_recipient];
    return _baseVestedAmount(_claim, _referenceTs);
}
```
changed to 
```
function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
    return _baseVestedAmount(claims[_recipient], _referenceTs);
}
```


**hasNoClaim modifier is used once (can be inlined)**
* Lines [123](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol?plain=1#L123), [253](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol?plain=1#L253)

**`++i` is cheaper than `i++`**

* Line [353](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol?plain=1#L353)

Suggestion: change `for (uint256 i = 0; i < length; i++)` to `for (uint256 i; i < length; ++i)`

**Cache variables to save an SLOAD**

* `usrClaim.amountWithdrawn` ([374-377](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L374-L377))
* `_claim.amountWithdrawn` ([426-429](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L426-L429))