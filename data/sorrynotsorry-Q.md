# QA (LOW & NON-CRITICAL)

## [L-01] Consider making contracts Pausable
The contracts are bringing many risks including how the token distribution is carried out. Making the contracts pausable would at least prevent malicious actions to be carried out in all projects using VTVL contracts if any zero-day vulnerability occurs.


## [L-02] There should be an admins array
AccessPortected.sol has setAdmin function that can add or remove admins for the protocol. Since there would be lots of projects using these contracts, it would be ideal to implement a require statement to check the number of admins, so that if the admins.length = 1, that admin should not be removed by accident. If the last admin is removed accidentally, the whole vesting contract would be in an irreversible position.

```solidity
    function setAdmin(address admin, bool isEnabled) public onlyAdmin {
        require(admin != address(0), "INVALID_ADDRESS");
        _admins[admin] = isEnabled;
        emit AdminAccessSet(admin, isEnabled);
    }
```

## [L-03] Check of code existence 
VTVLVesting.sol's constructor sets the token address to be vested for. However, there is no code existence checked for the token in order to prevent any mistaken input.

```solidity
    constructor(IERC20 _tokenAddress) {
        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
        tokenAddress = _tokenAddress;
    }
```
[Permalink](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L81-L84)

## [L-04] Logic error at `_baseVestedAmount` function
The expression should be as below;
```solidity
            if(_referenceTs >= _claim.endTimestamp) {
                _referenceTs = _claim.endTimestamp;
            }
```
rather than;

```solidity
            if(_referenceTs > _claim.endTimestamp) {
                _referenceTs = _claim.endTimestamp;
            }
```
[Permalink](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L154-L156)


## [L-05] linearVestAmount data type
VTVLVesting's `linearVestAmount` variable is of uint112 type. However, it can't answer the needs if the underlying token has an enormous supply greater than this amount