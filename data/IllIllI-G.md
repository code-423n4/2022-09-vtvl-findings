## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Save gas by not requring non-zero interval if no linear amount | 1 | 17100 |
| [G&#x2011;02] | Results of calls to `_msgSender()` not cached | 4 | 64 |
| [G&#x2011;03] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 7 | 840 |
| [G&#x2011;04] | State variables should be cached in stack variables rather than re-reading them from storage | 1 | 97 |
| [G&#x2011;05] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 4 | 452 |
| [G&#x2011;06] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 4 | 340 |
| [G&#x2011;07] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 1 | 60 |
| [G&#x2011;08] | Optimize names to save gas | 3 | 66 |
| [G&#x2011;09] | Using `bool`s for storage incurs overhead | 1 | 20000 |
| [G&#x2011;10] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 1 | 10 |
| [G&#x2011;11] | Splitting `require()` statements that use `&&` saves gas | 1 | 3 |
| [G&#x2011;12] | Don't compare boolean expressions to boolean literals | 1 | 9 |
| [G&#x2011;13] | Use custom errors rather than `revert()`/`require()` strings to save gas | 24 | - |
| [G&#x2011;14] | Functions guaranteed to revert when called by normal users can be marked `payable` | 7 | 147 |
| [G&#x2011;15] | Don't use `_msgSender()` if not supporting EIP-2771 | 13 | 208 |

Total: 73 instances over 15 issues with **39396 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values




## Gas Optimizations

### [G&#x2011;01]  Save gas by not requring non-zero interval if no linear amount
If there is no linear amount, a Gsset for the claim's interval can be converted to a Gsreset, saving **17100 gas**

*There is 1 instance of this issue:*
```solidity
File: /contracts/VTVLVesting.sol

263          require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
264:         require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263-L264

### [G&#x2011;02]  Results of calls to `_msgSender()` not cached
Saves at least **16 gas** per call skipped

*There are 4 instances of this issue:*
```solidity
File: /contracts/VTVLVesting.sol

371:         uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));

388:         tokenAddress.safeTransfer(_msgSender(), amountRemaining);

391:         emit Claimed(_msgSender(), amountRemaining);

410:         emit AdminWithdrawn(_msgSender(), _amountRequested);

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L371

### [G&#x2011;03]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 7 instances of this issue:*
```solidity
File: contracts/VTVLVesting.sol

/// @audit _recipients
/// @audit _startTimestamps
/// @audit _endTimestamps
/// @audit _cliffReleaseTimestamps
/// @audit _releaseIntervalsSecs
/// @audit _linearVestAmounts
/// @audit _cliffAmounts
333       function createClaimsBatch(
334           address[] memory _recipients, 
335           uint40[] memory _startTimestamps, 
336           uint40[] memory _endTimestamps, 
337           uint40[] memory _cliffReleaseTimestamps, 
338           uint40[] memory _releaseIntervalsSecs, 
339           uint112[] memory _linearVestAmounts, 
340           uint112[] memory _cliffAmounts) 
341:          external onlyAdmin {

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L333-L341

### [G&#x2011;04]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There is 1 instance of this issue:*
```solidity
File: contracts/token/VariableSupplyERC20Token.sol

/// @audit mintableSupply on line 40
41:               require(amount <= mintableSupply, "INVALID_AMOUNT");

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41

### [G&#x2011;05]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There are 4 instances of this issue:*
```solidity
File: contracts/token/VariableSupplyERC20Token.sol

43:               mintableSupply -= amount;

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L43

```solidity
File: contracts/VTVLVesting.sol

301:          numTokensReservedForVesting += allocatedAmount; // track the allocated amount

383:          numTokensReservedForVesting -= amountRemaining;

433:          numTokensReservedForVesting -= amountRemaining; // Reduces the allocation

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L301

### [G&#x2011;06]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There are 4 instances of this issue:*
```solidity
File: contracts/VTVLVesting.sol

/// @audit require() on line 262
264:          require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

/// @audit require() on line 374
377:          uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;

/// @audit require() on line 426
429:          uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;

/// @audit if-condition on line 166
167:                  uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L264

### [G&#x2011;07]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There is 1 instance of this issue:*
```solidity
File: contracts/VTVLVesting.sol

353:          for (uint256 i = 0; i < length; i++) {

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353

### [G&#x2011;08]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 3 instances of this issue:*
```solidity
File: contracts/AccessProtected.sol

/// @audit isAdmin(), setAdmin()
11:   abstract contract AccessProtected is Context {

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L11

```solidity
File: contracts/token/VariableSupplyERC20Token.sol

/// @audit mint()
10:   contract VariableSupplyERC20Token is ERC20, AccessProtected {

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L10

```solidity
File: contracts/VTVLVesting.sol

/// @audit getClaim(), vestedAmount(), finalVestedAmount(), claimableAmount(), allVestingRecipients(), numVestingRecipients(), createClaim(), createClaimsBatch(), withdraw(), withdrawAdmin(), revokeClaim(), withdrawOtherToken()
11:   contract VTVLVesting is Context, AccessProtected {

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L11

### [G&#x2011;09]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There is 1 instance of this issue:*
```solidity
File: contracts/AccessProtected.sol

12:       mapping(address => bool) private _admins; // user address => admin? mapping

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L12

### [G&#x2011;10]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There is 1 instance of this issue:*
```solidity
File: contracts/VTVLVesting.sol

353:          for (uint256 i = 0; i < length; i++) {

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353

### [G&#x2011;11]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There is 1 instance of this issue:*
```solidity
File: contracts/VTVLVesting.sol

344           require(_startTimestamps.length == length &&
345                   _endTimestamps.length == length &&
346                   _cliffReleaseTimestamps.length == length &&
347                   _releaseIntervalsSecs.length == length &&
348                   _linearVestAmounts.length == length &&
349                   _cliffAmounts.length == length, 
350                   "ARRAY_LENGTH_MISMATCH"
351:          );

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L351

### [G&#x2011;12]  Don't compare boolean expressions to boolean literals
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

*There is 1 instance of this issue:*
```solidity
File: contracts/VTVLVesting.sol

111:          require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111

### [G&#x2011;13]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 24 instances of this issue:*
```solidity
File: contracts/AccessProtected.sol

25:           require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");

40:           require(admin != address(0), "INVALID_ADDRESS");

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L25

```solidity
File: contracts/token/FullPremintERC20Token.sol

11:           require(supply_ > 0, "NO_ZERO_MINT");

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11

```solidity
File: contracts/token/VariableSupplyERC20Token.sol

27:           require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

37:           require(account != address(0), "INVALID_ADDRESS");

41:               require(amount <= mintableSupply, "INVALID_AMOUNT");

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27

```solidity
File: contracts/VTVLVesting.sol

82:           require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");

107:          require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

111:          require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

129:          require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");

255:          require(_recipient != address(0), "INVALID_ADDRESS");

256:          require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both

257:          require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

262:          require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp

263:          require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

264:          require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

270           require( 
271               (
272                   _cliffReleaseTimestamp > 0 && 
273                   _cliffAmount > 0 && 
274                   _cliffReleaseTimestamp <= _startTimestamp
275               ) || (
276                   _cliffReleaseTimestamp == 0 && 
277                   _cliffAmount == 0
278:          ), "INVALID_CLIFF");

295:          require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

344           require(_startTimestamps.length == length &&
345                   _endTimestamps.length == length &&
346                   _cliffReleaseTimestamps.length == length &&
347                   _releaseIntervalsSecs.length == length &&
348                   _linearVestAmounts.length == length &&
349                   _cliffAmounts.length == length, 
350                   "ARRAY_LENGTH_MISMATCH"
351:          );

374:          require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

402:          require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");

426:          require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

447:          require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor

449:          require(bal > 0, "INSUFFICIENT_BALANCE");

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L82

### [G&#x2011;14]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 7 instances of this issue:*
```solidity
File: contracts/AccessProtected.sol

39:       function setAdmin(address admin, bool isEnabled) public onlyAdmin {

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39

```solidity
File: contracts/token/VariableSupplyERC20Token.sol

36:       function mint(address account, uint256 amount) public onlyAdmin {

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L36

```solidity
File: contracts/VTVLVesting.sol

317       function createClaim(
318               address _recipient, 
319               uint40 _startTimestamp, 
320               uint40 _endTimestamp, 
321               uint40 _cliffReleaseTimestamp, 
322               uint40 _releaseIntervalSecs, 
323               uint112 _linearVestAmount, 
324               uint112 _cliffAmount
325:                  ) external onlyAdmin {

333       function createClaimsBatch(
334           address[] memory _recipients, 
335           uint40[] memory _startTimestamps, 
336           uint40[] memory _endTimestamps, 
337           uint40[] memory _cliffReleaseTimestamps, 
338           uint40[] memory _releaseIntervalsSecs, 
339           uint112[] memory _linearVestAmounts, 
340           uint112[] memory _cliffAmounts) 
341:          external onlyAdmin {

398:      function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    

418:      function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {

446:      function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L317-L325

### [G&#x2011;15]  Don't use `_msgSender()` if not supporting EIP-2771
Use `msg.sender` if the code does not implement [EIP-2771 trusted forwarder](https://eips.ethereum.org/EIPS/eip-2771) support

*There are 13 instances of this issue:*
```solidity
File: contracts/AccessProtected.sol

17:           _admins[_msgSender()] = true;

18:           emit AdminAccessSet(_msgSender(), true);

25:           require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L17

```solidity
File: contracts/token/FullPremintERC20Token.sol

12:           _mint(_msgSender(), supply_);

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L12

```solidity
File: contracts/token/VariableSupplyERC20Token.sol

32:               mint(_msgSender(), initialSupply_);

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L32

```solidity
File: contracts/VTVLVesting.sol

367:          Claim storage usrClaim = claims[_msgSender()];

371:          uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));

388:          tokenAddress.safeTransfer(_msgSender(), amountRemaining);

391:          emit Claimed(_msgSender(), amountRemaining);

364:      function withdraw() hasActiveClaim(_msgSender()) external {

407:          tokenAddress.safeTransfer(_msgSender(), _amountRequested);

410:          emit AdminWithdrawn(_msgSender(), _amountRequested);

450:          _otherTokenAddress.safeTransfer(_msgSender(), bal);

```
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L367

