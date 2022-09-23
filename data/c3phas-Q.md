
## QA
## Known vulnerabilities exist in currently used @openzeppelin/contracts version

As some [known vulnerabilities](https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts/4.5.0) exist in the current ```@openzeppelin/contracts``` version, consider updating ```package.json``` with at least ```@openzeppelin/contracts@4.7.3``` here:

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/package.json#L7

```solidity
File: /package.json
7:    "@openzeppelin/contracts": "^4.5.0",
```

### public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39
```solidity
File: /contracts/AccessProtected.sol
39:     function setAdmin(address admin, bool isEnabled) public onlyAdmin {
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398
```solidity
File: /contracts/VTVLVesting.sol
398:    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    
```

### Lock pragmas to specific compiler version
Contracts should be deployed with the same compiler version and flags that they have been tested the most with. Locking the pragma helps ensure that contracts do not accidentally get deployed using, for example, the latest compiler which may have higher risks of undiscovered bugs. Contracts may also be deployed by others and the pragma indicates the compiler version intended by the original authors.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L2
```solidity
File: /contracts/token/VariableSupplyERC20Token.sol
2: pragma solidity ^0.8.14;
```

### Typos/Grammer
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L96
```solidity
File: /contracts/VTVLVesting.sol
//@audit: an user --> a user
96:    @notice This modifier requires that an user has a claim attached.
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L14
```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

//@audit: A ERC20 --> An ERC20
8:     @notice A ERC20 token contract that allows minting at will, with limited or unlimited supply.

//@audit: A ERC20 --> An ERC20
14:    @notice A ERC20 token contract that allows minting at will, with limited or unlimited supply. No burning possible
```

### Remove commented code
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L9
```solidity
File: /contracts/token/FullPremintERC20Token.sol
9:    // uint constant _initialSupply = 100 * (10**18);
```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L114-L115
```solidity
File: /contracts/VTVLVesting.sol
114:        // require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
115:        // require(_claim.endTimestamp > 0, "NO_END_TIMESTAMP");
```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L133-L138
```solidity
File: /contracts/VTVLVesting.sol
133:        // require(_claim.isActive == false, "CLAIM_ALREADY_EXISTS");

136:        // require(_claim.endTimestamp == 0, "CLAIM_ALREADY_EXISTS");
137:        // require(_claim.linearVestAmount + _claim.cliffAmount == 0, "CLAIM_ALREADY_EXISTS");
138:        // require(_claim.amountWithdrawn == 0, "CLAIM_ALREADY_EXISTS");
```

### Open todos
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266
```solidity
File: /contracts/VTVLVesting.sol
266:        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
```

### Natspec is incomplete/Missing
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L415-L418
```solidity
File: /contracts/VTVLVesting.sol

//@audit: Missing @param _recipient
415:    @notice Allow an Owner to revoke a claim that is already active.
416:    @dev The requirement is that a claim exists and that it's active.
417:    */ 
418:    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L333-L341
```solidity
File: /contracts/VTVLVesting.sol

//@audit: Missing @param _recipients, @param _startTimestamps,@param _endTimestamps, @param _cliffReleaseTimestamps, @param _releaseIntervalsSecs,@param _linearVestAmounts, //@param _cliffAmounts
333:    function createClaimsBatch(
334:        address[] memory _recipients, 
335:        uint40[] memory _startTimestamps, 
336:        uint40[] memory _endTimestamps, 
337:        uint40[] memory _cliffReleaseTimestamps, 
338:        uint40[] memory _releaseIntervalsSecs, 
339:        uint112[] memory _linearVestAmounts, 
340:        uint112[] memory _cliffAmounts) 
341:        external onlyAdmin {
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L36
```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

//@audit: Missing @param account, @param amount
36:    function mint(address account, uint256 amount) public onlyAdmin {
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L29
```solidity
File: /contracts/AccessProtected.sol

//@audit: Missing @param _addressToCheck
29:    function isAdmin(address _addressToCheck) external view returns (bool) {
```

### Incorrect revert string used
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107
```solidity
File: /contracts/VTVLVesting.sol

//@audit: Should the string be "INVALID_START_TIMESTAMP" instead or probably change it not be similar with the one on the next statement
107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

```
The revert string used in above matches another one in the same modifier which does the actual check reffered to by the string. see below
```solidity
File: /contracts/VTVLVesting.sol
111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
```


## Code Structure Deviates From Best-Practice

### Description:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier. Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. 
Some constructs deviate from this recommended best-practice: Modifiers are in the middle of contracts. External/public functions are mixed with internal/private ones.

**The following contract has modifiers coming in between functions, external function are mixed with internal/private  ones**
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol

## Lack of event emission for critical  functions

Similar to how we emit an event when withdrawing unallocated tokens, we should also emit an event for the following

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L446-L451
```solidity
File: /contracts/VTVLVesting.sol
446:    function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {
447:        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
448:        uint256 bal = _otherTokenAddress.balanceOf(address(this));
449:        require(bal > 0, "INSUFFICIENT_BALANCE");
450:        _otherTokenAddress.safeTransfer(_msgSender(), bal);
    }
```
