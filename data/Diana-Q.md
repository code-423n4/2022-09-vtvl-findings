## L-01  CRITICAL CHANGES SHOULD USE TWO-STEP PROCEDURE

The critical procedures should be two step process.

### Proof of Concept

There is 1 instance of this issue:

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39-L43

```
File: contracts/AccessProtected.sol

function setAdmin(address admin, bool isEnabled) public onlyAdmin {
        require(admin != address(0), "INVALID_ADDRESS");
        _admins[admin] = isEnabled;
        emit AdminAccessSet(admin, isEnabled);
    }
```

### Recommended Mitigation Steps

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

----------

## L-02  NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2

```
File: contracts/token/VariableSupplyERC20Token.sol    Line: 2

pragma solidity ^0.8.14;
```

### Recommended Mitigation Steps

Lock the pragma version

------------
## L-03 MISSING EVENTS FOR IMPORTANT FUNCTIONS

### Proof of Concept

There are 3 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36-L46

```
File: contracts/token/VariableSupplyERC20Token.sol #1

function mint(address account, uint256 amount) public onlyAdmin {
        require(account != address(0), "INVALID_ADDRESS");
        // If we're using maxSupply, we need to make sure we respect it
        // mintableSupply = 0 means mint at will
        if(mintableSupply > 0) {
            require(amount <= mintableSupply, "INVALID_AMOUNT");
            // We need to reduce the amount only if we're using the limit, if not just leave it be
            mintableSupply -= amount;
        }
        _mint(account, amount);
    }
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L317-L327

```
File: contracts/VTVLVesting.sol #2

function createClaim(
            address _recipient, 
            uint40 _startTimestamp, 
            uint40 _endTimestamp, 
            uint40 _cliffReleaseTimestamp, 
            uint40 _releaseIntervalSecs, 
            uint112 _linearVestAmount, 
            uint112 _cliffAmount
                ) external onlyAdmin {
        _createClaimUnchecked(_recipient, _startTimestamp, _endTimestamp, _cliffReleaseTimestamp, _releaseIntervalSecs, _linearVestAmount, _cliffAmount);
    }
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L446-L451

```
File: contracts/VTVLVesting.sol #3

function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
        uint256 bal = _otherTokenAddress.balanceOf(address(this));
        require(bal > 0, "INSUFFICIENT_BALANCE");
        _otherTokenAddress.safeTransfer(_msgSender(), bal);
    }
```

-------------

## N-01  USE A MORE RECENT VERSION OF SOLIDITY

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives security fixes. Furthermore, breaking changes as well as new features are introduced regularly.

### Proof of Concept

There are 4 instances of this issue.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L2

```
File: contracts/token/FullPremintERC20Token.sol    #1

pragma solidity 0.8.14;
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2

```
File: contracts/token/VariableSupplyERC20Token.sol    #2

pragma solidity ^0.8.14;
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L2

```
File: contracts/AccessProtected.sol    #3

pragma solidity 0.8.14;
```

https://github.com/code-423n4/2022-09-tribe/blob/main/contracts/shutdown/redeem/TribeRedhttps://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L2

```
File: contracts/VTVLVesting.sol    #4

pragma solidity 0.8.14;
```


### Recommended Mitigation Steps

Update to the latest released version of Solidity

-----------

## N-02  NATSPEC IS MISSING/INCOMPLETE

There are 5 instances of this issue

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L6-L14

```
File: contracts/token/FullPremintERC20Token.sol #1

// Mint everything at once
// VariableSupplyERC20Token could be used instead, but it needs to track available to mint supply (extra slot)
contract FullPremintERC20Token is ERC20 {
    // uint constant _initialSupply = 100 * (10**18);
    constructor(string memory name_, string memory symbol_, uint256 supply_) ERC20(name_, symbol_) {
        require(supply_ > 0, "NO_ZERO_MINT");
        _mint(_msgSender(), supply_);
    }
}
```

Missing: `@param name_`, `@param symbol_`, `@param supply_`

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36-L46

```
File: contracts/token/VariableSupplyERC20Token.sol #2

function mint(address account, uint256 amount) public onlyAdmin {
        require(account != address(0), "INVALID_ADDRESS");
        // If we're using maxSupply, we need to make sure we respect it
        // mintableSupply = 0 means mint at will
        if(mintableSupply > 0) {
            require(amount <= mintableSupply, "INVALID_AMOUNT");
            // We need to reduce the amount only if we're using the limit, if not just leave it be
            mintableSupply -= amount;
        }
        _mint(account, amount);
    }
```

Missing: `@param account`, `@param amount`

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L19-L27

```
File: contracts/VTVLVesting.sol #3

/**
    @notice How many tokens are already allocated to vesting schedules.
    @dev Our balance of the token must always be greater than this amount.
    * Otherwise we risk some users not getting their shares.
    * This gets reduced as the users are paid out or when their schedules are revoked (as it is not reserved any more).
    * In other words, this represents the amount the contract is scheduled to pay out at some point if the 
    * owner were to never interact with the contract.
    */
    uint112 public numTokensReservedForVesting = 0;
```

Missing: `@param numTokensReservedForVesting`

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L414-L437

```
File: contracts/VTVLVesting.sol #4

/** 
    @notice Allow an Owner to revoke a claim that is already active.
    @dev The requirement is that a claim exists and that it's active.
    */ 
    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {
        // Fetch the claim
        Claim storage _claim = claims[_recipient];
        // Calculate what the claim should finally vest to
        uint112 finalVestAmt = finalVestedAmount(_recipient);

        // No point in revoking something that has been fully consumed
        // so require that there be unconsumed amount
        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

        // The amount that is "reclaimed" is equal to the total allocation less what was already withdrawn
        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;

        // Deactivate the claim, and release the appropriate amount of tokens
        _claim.isActive = false;    // This effectively reduces the liability by amountRemaining, so we can reduce the liability numTokensReservedForVesting by that much
        numTokensReservedForVesting -= amountRemaining; // Reduces the allocation

        // Tell everyone a claim has been revoked.
        emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);
    }
```

Missing: `@param finalVestAmt`, `@param amountRemaining`

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L439-L451

```
File: contracts/VTVLVesting.sol #5

/**
    @notice Withdraw a token which isn't controlled by the vesting contract.
    @dev This contract controls/vests token at "tokenAddress". However, someone might send a different token. 
    To make sure these don't get accidentally trapped, give admin the ability to withdraw them (to their own address).
    Note that the token to be withdrawn can't be the one at "tokenAddress".
    @param _otherTokenAddress - the token which we want to withdraw
     */
    function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
        uint256 bal = _otherTokenAddress.balanceOf(address(this));
        require(bal > 0, "INSUFFICIENT_BALANCE");
        _otherTokenAddress.safeTransfer(_msgSender(), bal);
    }
```

Missing: `@param bal`

--------------

## N-03  Event is missing indexed fields

### Proof of Concept

There are 5 instances of this issue

Each `event` should use three `indexed` fields if there are three or more fields

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L14

```
File: contracts/AccessProtected.sol    Line: 14

event AdminAccessSet(address indexed _admin, bool _enabled);
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
File: contracts/VTVLVesting.sol

59: event ClaimCreated(address indexed _recipient, Claim _claim);
64: event Claimed(address indexed _recipient, uint112 _withdrawalAmount);
69: event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
74: event AdminWithdrawn(address indexed _recipient, uint112 _amountRequested);
```

-----------
