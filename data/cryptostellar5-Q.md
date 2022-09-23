### L-01 CRITICAL CHANGES SHOULD USE TWO-STEP PROCEDURE

*Total Instances Identified: 1*

The critical procedures should be two step process.

### Proof of Concept

Navigate to 
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39-L43

```
    function setAdmin(address admin, bool isEnabled) public onlyAdmin {
        require(admin != address(0), "INVALID_ADDRESS"); 
        _admins[admin] = isEnabled;
        emit AdminAccessSet(admin, isEnabled);
    } 
```

### Recommended Mitigation Steps

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### L-02 MISSING EVENTS FOR IMPORTANT FUNCTIONS

*Total Instances Identified: 3*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36-L46

```
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

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L316-L327

```
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
function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin { 
        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor  
        uint256 bal = _otherTokenAddress.balanceOf(address(this));
        require(bal > 0, "INSUFFICIENT_BALANCE");
        _otherTokenAddress.safeTransfer(_msgSender(), bal);
    } 
```


### L-03 MISSING ZERO-ADDRESS CHECK

*Total Instances Identified: 2*

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L317-L327

```
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

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L333-L351

```
function createClaimsBatch(
        address[] memory _recipients, 
        uint40[] memory _startTimestamps,  
        uint40[] memory _endTimestamps,   
        uint40[] memory _cliffReleaseTimestamps,   
        uint40[] memory _releaseIntervalsSecs,   
        uint112[] memory _linearVestAmounts,   
        uint112[] memory _cliffAmounts)   
        external onlyAdmin {
        
        uint256 length = _recipients.length;
        require(_startTimestamps.length == length &&
                _endTimestamps.length == length &&
                _cliffReleaseTimestamps.length == length &&
                _releaseIntervalsSecs.length == length &&
                _linearVestAmounts.length == length &&
                _cliffAmounts.length == length, 
                "ARRAY_LENGTH_MISMATCH"
        );
```


### L-04 FLOATING PRAGMA VERSION SHOULD NOT BE USED

*Total Instances Identified: 1*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)



### N-01 CONSIDER MAKING CONTRACT PAUSABLE TO HAVE SOME PROTECTION AGAINST ONGOING EXPLOITS

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L11


```
contract VTVLVesting is Context, AccessProtected
```


### N-02 NATSPEC IS INCOMPLETE

*Total Instances Identified: 5*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27
Missing `@param` *numTokensReservedForVesting*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L422
Missing `@param` *finalVestAmt*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L448
Missing `@param` *bal*

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L10
Missing `@param`  *name*, *symbol*, *supply* 

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36
Missing `@param` *account*, *amount*


### N-03 EVENT IS MISSING INDEXED FIELDS

*Total Instances Identified: 5*

Each `event` should use three `indexed` fields if there are three or more fields

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L14

```
event AdminAccessSet(address indexed _admin, bool _enabled);
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol

```
59: event ClaimCreated(address indexed _recipient, Claim _claim);

64: event Claimed(address indexed _recipient, uint112 _withdrawalAmount);

69: event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);

74: event AdminWithdrawn(address indexed _recipient, uint112 _amountRequested);
```


### N-04 USE LATEST SOLIDITY VERSION

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives [security fixes](https://github.com/ethereum/solidity/security/policy#supported-versions). Furthermore, breaking changes as well as new features are introduced regularly.
Latest Version is 0.8.17

This applies to all the in-scope contracts
