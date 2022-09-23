## if _linearVestAmount == 0, _startTimestamp can't be same as _endTimestamp

You have noted that "if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?" but it contradicts with this check

```
require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
```

if (_startTimestamp == _endTimestamp) it will revert with "INVALID_END_TIMESTAMP"

To fix this you should 

```
require(_startTimestamp < _endTimestamp || (_linearVestAmount == 0 && _startTimestamp ==_endTimestamp) , "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
```