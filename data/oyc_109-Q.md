
## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::2 => pragma solidity ^0.8.14;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-09-vtvl/contracts/VTVLVesting.sol::217 => return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
2022-09-vtvl/contracts/VTVLVesting.sol::371 => uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
2022-09-vtvl/contracts/VTVLVesting.sol::436 => emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);
```

## [L-03] Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

```
2022-09-vtvl/contracts/VTVLVesting.sol::266 => // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
```

## [L-04] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-09-vtvl/contracts/token/FullPremintERC20Token.sol::12 => _mint(_msgSender(), supply_);
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::45 => _mint(account, amount);
```

## [N-01] Missing NatSpec

Code should include NatSpec

```
2022-09-vtvl/contracts/token/FullPremintERC20Token.sol::1 => //SPDX-License-Identifier: Unlicense
```

## [N-02] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public.

```
2022-09-vtvl/contracts/AccessProtected.sol::39 => function setAdmin(address admin, bool isEnabled) public onlyAdmin {
2022-09-vtvl/contracts/VTVLVesting.sol::398 => function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
```

## [N-03] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2022-09-vtvl/contracts/VTVLVesting.sol::241 => @param _releaseIntervalSecs - The release interval for the linear vesting. If this is, for example, 60, that means that the linearly vested amount gets released every 60 seconds.
2022-09-vtvl/contracts/VTVLVesting.sol::260 => // -> Conclusion: we want to allow this, for founders that might have forgotten to add some users, or to avoid issues with transactions not going through because of discoordination between block.timestamp and sender's local time
2022-09-vtvl/contracts/VTVLVesting.sol::313 => @param _releaseIntervalSecs - The release interval for the linear vesting. If this is, for example, 60, that means that the linearly vested amount gets released every 60 seconds.
2022-09-vtvl/contracts/VTVLVesting.sol::354 => _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
2022-09-vtvl/contracts/VTVLVesting.sol::432 => _claim.isActive = false;    // This effectively reduces the liability by amountRemaining, so we can reduce the liability numTokensReservedForVesting by that much
```
