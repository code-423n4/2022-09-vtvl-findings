### Low Risk Issues

#### [LR01] Improve readability

Change setAdmin to setAdminStatus for readability. Set admin could be misunderstood as setting the address to be admin, but it instead changes the admin status of the address, from true to false or false to true.

```solidity
File: contracts/AccessProtected.sol

      function setAdmin(address admin, bool isEnabled) public onlyAdmin {
          require(admin != address(0), "INVALID_ADDRESS");
          _admins[admin] = isEnabled;
          emit AdminAccessSet(admin, isEnabled);
      }
```

#### [LR02] Unused import

The openzeppelin Ownable contract is not used in the contract. It can be removed.

```solidity
import "@openzeppelin/contracts/access/Ownable.sol";
```

#### [LR03] Multiply then divide

Its important to multiply before dividing to avoid rounding errors. A division can lead to 0 making the multiplication not longer work because of rounding down.

```solidity
contracts/VTVLVesting.sol#L169 => uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
```


### Non-critical Issues

#### [NC01] Use external instead of public for functions that are not called internally

```solidity
contracts/VTVLVesting.sol#L398-L411 => function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {  
contracts/AccessProtected.sol#L39-L43 => function setAdmin(address admin, bool isEnabled) public onlyAdmin {
```

#### [NC02] Check for true

There is no need to check if active is == true

```solidity
File: contracts/VTVLVesting.sol#L111

require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

```

Solution:

```solidity

require(_claim.isActive, "NO_ACTIVE_CLAIM");

```
