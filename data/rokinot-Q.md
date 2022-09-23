## Low

### Contracts can have no administrators, leaving tokens meant for vesting to be stuck forever

An administrator can revoke it's own power calling `setAdmin()` with it's own address and boolean as false. This will leave unallocated tokens and tokens accidentally sent to the contract stuck forever, since it will be impossible to set a new administrator.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39-L43

Consider reverting the setAdmin transaction if there's only one administrator set. It's worthy noting this may also be considered a feature rather than a bug.