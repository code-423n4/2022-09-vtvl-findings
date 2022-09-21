## Design / Architecture
- Currently, the cliff date cannot be after the vesting start date. In my experience, there are scenarios where companies have a cliff date during a vesting period. Such situations currently could not be handled by VTVL.
- It is not possible for the admin to increase a claim afterwards. I think that this could be limiting in practice. For instance, it can happen that the vesting details of an employee are changed after a promotion.
- After one claim has ended, it is not possible to create another one for the same address, which is also quite limiting in my opinion. Often times, a new vesting period is agreed upon a company and an employee when the previous has ended. Currently, the employee would need to generate another address for that, which is not very user friendly.

## Technical Issues
- Using a `uint112` for amounts (especially for `numTokensReservedForVesting`, which is the global sum) can be insufficient for certain tokens. Consider a token with 24 decimals and where one token has a value of 0.0000001 USD. The maximum amount for which the protocol can be used is 2^112 / 10^24 * 0.0000001 USD = 519.23 USD, which is extremely low.