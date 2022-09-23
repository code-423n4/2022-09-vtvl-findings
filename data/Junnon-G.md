## Ineffective `storage` useage (up to -97 gas)

in EVM gas use for access the `storage` is 100 gas, it's really contrast from access `memory` or `calldata` that only use 3 gas for `LOAD` and `STORE`. i recommended choose between `memory` and `calldata` depend on the useage instead of `storage`. here i recommended when to use what type:

1. `storage` use when only called twice or less in a function.
2. `memory` use when you need to change the data inside the variable.
3. `calldata` use when you only need to read the variable without changing data. 

for example here:
```js
///VTVLVesting.sol

    function withdraw() hasActiveClaim(_msgSender()) external {
        // Get the message sender claim - if any

        Claim storage usrClaim = claims[_msgSender()];///@audit SLOAD!!!

        // we can use block.timestamp directly here as reference TS, as the function itself will make sure to cap it to endTimestamp
        // Conversion of timestamp to uint40 should be safe since 48 bit allows for a lot of years.
        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));

        // Make sure we didn't already withdraw more that we're allowed.
        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW"); //@audit SLOAD!!!

        // Calculate how much can we withdraw (equivalent to the above inequality)
        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;//@audit SLOAD!!!

        // "Double-entry bookkeeping"
        // Carry out the withdrawal by noting the withdrawn amount, and by transferring the tokens.
        usrClaim.amountWithdrawn += amountRemaining;//@audit SSTORE!!!
        // Reduce the allocated amount since the following transaction pays out so the "debt" gets reduced
        numTokensReservedForVesting -= amountRemaining;
        
        // After the "books" are set, transfer the tokens
        // Reentrancy note - internal vars have been changed by now
        // Also following Checks-effects-interactions pattern
        tokenAddress.safeTransfer(_msgSender(), amountRemaining);

        // Let withdrawal known to everyone.
        emit Claimed(_msgSender(), amountRemaining);
    }

```

in code above we can see that there's 3 `SLOAD`(storage read) and 1 `SSTORE`(storage write) which cost 100 gas each we can make it more efficient with passing it in memory instead of storage like code bellow:

```js 
///VTVLVesting.sol

    function withdraw() hasActiveClaim(_msgSender()) external {
        // Get the message sender claim - if any

        Claim memory usrClaim = claims[_msgSender()];///@audit changed to memory SLOAD 100 gas + MSTORE 3 gas 

        // we can use block.timestamp directly here as reference TS, as the function itself will make sure to cap it to endTimestamp
        // Conversion of timestamp to uint40 should be safe since 48 bit allows for a lot of years.
        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));

        // Make sure we didn't already withdraw more that we're allowed.
        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW"); //@audit MLOAD!!! 3 gas

        // Calculate how much can we withdraw (equivalent to the above inequality)
        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;//@audit MLOAD!!! 3 gas

        // "Double-entry bookkeeping"
        // Carry out the withdrawal by noting the withdrawn amount, and by transferring the tokens.
        usrClaim.amountWithdrawn += amountRemaining;//@audit MSTORE!!! 3 gas 
        // Reduce the allocated amount since the following transaction pays out so the "debt" gets reduced
        numTokensReservedForVesting -= amountRemaining;

        ///ADD THIS 
        claims[_msgSender()] = usrClaim; //@audit SSTORE 100 gas + MLOAD 3 gas
        ///
        
        // After the "books" are set, transfer the tokens
        // Reentrancy note - internal vars have been changed by now
        // Also following Checks-effects-interactions pattern
        tokenAddress.safeTransfer(_msgSender(), amountRemaining);

        // Let withdrawal known to everyone.
        emit Claimed(_msgSender(), amountRemaining);
    }
```

in example below we can make gas useage from 400 gas to 215 gas. it can be more efficient too for others instance below

### INSTANCE:
VTVLVesting.sol

1. VTVLVesting::hasActiveClaim() (Note: to `calldata`)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L106
2. VTVLVesting::hasNoClaim() (Note: to `calldata`)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L124
3. VTVLVesting::vestedAmount() (Note: to `calldata`)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L197
4. VTVLVesting::finalVestedAmount() (Note: to `calldata`)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L207
5. VTVLVesting::withdraw() (Note: to `memory`)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L364-L392
6. VTVLVesting::revokeClaim() (Note: to `memory`)
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L418-L437

## Inefficient Loop 

Loop is gas costly when operated in EVM. Loop can be optimize with this steps. 
1. Using `++i` instead of `i++` 
2. Avoid declaring default value of `uint` for `i`
3. Avoid using `array.length` for parameter 
4. using `unchecked` math for `i`

example: 
```js 
///VTVLVesting.sol 

    /// existing 
    for (uint256 i = 0; i < length; i++) {
        _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
    }

    /// cheaper
    for (uint256 i; i < length) {
        _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);

        unchecked{
            ++i;
        }
    }
```

### INSTANCE
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353-L355

## Considering using custom error instead of `require`

Custom error is a feature from solidity that available from solidity v0.8.4. Custom error can make the contract more convinient and gas efficient, it's also easier to use across the contract. require can be more informational for user but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

example
```js
    ///using require
    require(addr != address(0), "address zero");

    //custom error
    error AddressZero();

    if(addr == address(0)) revert AddressZero();

```

### Instance:
VTVLVesting.sol
1. VTVLVesting::constructor()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L82
2. VTVLVesting::hasActiveClaim()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107-L111
3. VTVLVesting::hasNoClaim()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L129
4. VTVLVesting::_createClaimUnchecked()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255-L278
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L295
5. VTVLVesting::createClaimsBatch()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344
6. VTVLVesting::withdraw()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374
7. VTVLVesting::withdrawAdmin()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L402
8. VTVLVesting::revokeClaim()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L426
9. VTVLVesting::withdrawOtherToken()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L447-L449

AccessProtected.sol
1. AccessProtected::onlyAdmin()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L25
2. AccessProtected::setAdmin()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L40

VariableSupplyERC20Token.sol
1. VariableSupplyERC20Token::constructor()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27
2. VariableSupplyERC20Token::mint()
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L37-L41

FullPremintERC20Token.sol
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11

