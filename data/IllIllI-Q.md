## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | `_msgSender()` used without implementing EIP-2771, or being intermediate contract | 1 |
| [L&#x2011;02] | Code does not follow Check-Effects-Interaction best practice | 1 |
| [L&#x2011;03] | Open TODOs | 1 |

Total: 3 instances over 3 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `public` functions not called by the contract should be declared `external` instead | 1 |
| [N&#x2011;02] | Lines are too long | 5 |
| [N&#x2011;03] | Non-library/interface files should use fixed compiler versions, not floating ones | 1 |
| [N&#x2011;04] | File is missing NatSpec | 1 |
| [N&#x2011;05] | NatSpec is incomplete | 5 |
| [N&#x2011;06] | Event is missing `indexed` fields | 5 |

Total: 18 instances over 6 issues


## Low Risk Issues

### [L&#x2011;01]  `_msgSender()` used without implementing EIP-2771, or being intermediate contract
The [EIP-2771](https://eips.ethereum.org/EIPS/eip-2771) states that "To provide this discovery mechanism a Recipient contract MUST implement this function:" and then outlines the requirements of the `isTrustedForwarder()` function. This contract does not provide such a function, so the contract does not properly follow the specification, and therefore this is a low-severity issue. The contract additionally extends OpenZeppelin's `Context` contract, whose comments note that it's meant for intermediate library contracts, rather than end-user contracts, and I confirmed with the sponsor that this contract is non-intermediate. If trusted forwarders are meant to be used, the contract should extend `ERC2771Context` instead, otherwise the contract should just use `msg.sender`

*There is 1 instance of this issue:*
```solidity
File: /contracts/VTVLVesting.sol

364      function withdraw() hasActiveClaim(_msgSender()) external {
365          // Get the message sender claim - if any
366  
367:         Claim storage usrClaim = claims[_msgSender()];

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L364-L367

### [L&#x2011;02]  Code does not follow Check-Effects-Interaction best practice
The code below makes an external call to `balanceOf()` after the `hasNoClaim` modifier has already passed. Normally this is fine because `balanceOf()` is a `view` function. However, it's possible that the token is a specially crafted token that's designed to re-enter (e.g. by using a fallback function to avoid compiler warnings about non-view function calls inside a view function). If such a contract does this, it's possible for the admin to overwrite the prior claim, and under-collateralize the contract. The check for an existing claim should occur after all external calls have been made, or the require should be moved to the end of the function

*There is 1 instance of this issue:*
```solidity
File: /contracts/VTVLVesting.sol

295          require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
296  
297          // Done with checks
298  
299          // Effects limited to lines below
300          claims[_recipient] = _claim; // store the claim
301          numTokensReservedForVesting += allocatedAmount; // track the allocated amount
302          vestingRecipients.push(_recipient); // add the vesting recipient to the list
303          emit ClaimCreated(_recipient, _claim); // let everyone know
304:     }

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L295-L304

### [L&#x2011;03]  Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

*There is 1 instance of this issue:*
```solidity
File: contracts/VTVLVesting.sol

266:          // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266

## Non-critical Issues

### [N&#x2011;01]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There is 1 instance of this issue:*
```solidity
File: contracts/VTVLVesting.sol

398:      function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398

### [N&#x2011;02]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 5 instances of this issue:*
```solidity
File: contracts/VTVLVesting.sol

241:      @param _releaseIntervalSecs - The release interval for the linear vesting. If this is, for example, 60, that means that the linearly vested amount gets released every 60 seconds.

260:          // -> Conclusion: we want to allow this, for founders that might have forgotten to add some users, or to avoid issues with transactions not going through because of discoordination between block.timestamp and sender's local time

313:      @param _releaseIntervalSecs - The release interval for the linear vesting. If this is, for example, 60, that means that the linearly vested amount gets released every 60 seconds.

354:              _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);

432:          _claim.isActive = false;    // This effectively reduces the liability by amountRemaining, so we can reduce the liability numTokensReservedForVesting by that much

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L241

### [N&#x2011;03]  Non-library/interface files should use fixed compiler versions, not floating ones

*There is 1 instance of this issue:*
```solidity
File: contracts/token/VariableSupplyERC20Token.sol

2:    pragma solidity ^0.8.14;

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L2

### [N&#x2011;04]  File is missing NatSpec

*There is 1 instance of this issue:*
```solidity
File: contracts/token/FullPremintERC20Token.sol


```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol

### [N&#x2011;05]  NatSpec is incomplete

*There are 5 instances of this issue:*
```solidity
File: contracts/VTVLVesting.sol

/// @audit Missing: '@return'
89        @param _recipient - the address for which we fetch the claim.
90         */
91:       function getClaim(address _recipient) external view returns (Claim memory) {

/// @audit Missing: '@return'
145       @param _referenceTs Timestamp for which we're calculating
146        */
147:      function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {

/// @audit Missing: '@return'
194       @dev Simply call the _baseVestedAmount for the claim in question
195       */
196:      function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {

/// @audit Missing: '@return'
204       @param _recipient - The address for whom we're calculating
205        */
206:      function finalVestedAmount(address _recipient) public view returns (uint112) {

/// @audit Missing: '@return'
213       @param _recipient - The address for whom we're calculating
214       */
215:      function claimableAmount(address _recipient) external view returns (uint112) {

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L89-L91

### [N&#x2011;06]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 5 instances of this issue:*
```solidity
File: contracts/AccessProtected.sol

14:       event AdminAccessSet(address indexed _admin, bool _enabled);

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L14

```solidity
File: contracts/VTVLVesting.sol

59:       event ClaimCreated(address indexed _recipient, Claim _claim); 

64:       event Claimed(address indexed _recipient, uint112 _withdrawalAmount); 

69:       event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);

74:       event AdminWithdrawn(address indexed _recipient, uint112 _amountRequested);

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L59

