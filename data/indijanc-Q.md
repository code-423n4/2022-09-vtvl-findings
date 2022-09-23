I would say the contracts are well written, with good comments and the functionality is protected with a good test suite. As mentioned in the contest description the admins / founders will have certain capabilities of managing the contract which will ultimately also need to be acompanied by certain trust. The contest description also states that the projects makes an effort to make any changes to claims transparent. Hence I would advise the following:

## Include admin / founder address in all claim changes and admin withdrawal events
`VTVLVesting` is open to have many admins, so it would make sense to include the admin address on all relevant events to increase transparency

[VTVLVesting.sol L59](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L59)
Consider including the address of the admin that created the claim for better transparency.

[VTVLVesting.sol L69](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L69)
Consider including the address of the admin that revoked the claim for better transparency.

[VTVLVesting.sol L74](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L74)
Consider including the address of the admin that withdrew. This is probably less important than the other two events emitting claim changes, but still may be valuable as there may be several admins associated with a vesting contract and would want to have this included in the event.

