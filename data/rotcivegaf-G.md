# Gas report

| G-N    |Issue|Instances|
|:------:|:----|:-------:|
| [G&#x2011;01] | No need to initialize variables with default values | 3 |
| [G&#x2011;02] | Use custom errors rather than `revert()`/`require()` strings to save gas | 24 |
| [G&#x2011;03] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 1 |
| [G&#x2011;04] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 1 |
| [G&#x2011;05] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 6 |
| [G&#x2011;06] | Overcheck if the account it is not `address(0)` | 1 |
| [G&#x2011;07] | The use of `_msgSender()` when there is no implementation of a meta transaction mechanism that uses it, such as EIP-2771, very slightly increases gas consumption | 13 |
| [G&#x2011;08] | `public` functions to `external` functions | 2 |
| [G&#x2011;09] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 1 |
| [G&#x2011;10] | Use `ClaimCreated` event to track the recipients of the vesting instead of storage in `vestingRecipients` array | 1 |
| [G&#x2011;11] | Redundant type casting | 1 |
| [G&#x2011;12] | Redundant check | 1 |
| [G&#x2011;13] | Overcalculate finalVestedAmount | 1 |

Total: 56 instances over 13 issues

## [G-01] No need to initialize variables with default values

In solidity all variables initialize in 0, address(0), false, etc.

```solidity
File: /contracts/VTVLVesting.sol

27     uint112 public numTokensReservedForVesting = 0;

148        uint112 vestAmt = 0;

353        for (uint256 i = 0; i < length; i++) {
```

## [G-02] Use custom errors rather than `revert()`/`require()` strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save [~50](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) gas each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

```solidity
File: /contracts/token/FullPremintERC20Token.sol

11        require(supply_ > 0, "NO_ZERO_MINT");
```

```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

27        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

37        require(account != address(0), "INVALID_ADDRESS");

41            require(amount <= mintableSupply, "INVALID_AMOUNT");
```

```solidity
File: /contracts/AccessProtected.sol

25        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");

40        require(admin != address(0), "INVALID_ADDRESS");
```

```solidity
File: /contracts/VTVLVesting.sol

82         require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");

107        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

111        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

129        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");

255        require(_recipient != address(0), "INVALID_ADDRESS");

256        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both

257        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

262        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp

263        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

265        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

270        require(
271            (
272                _cliffReleaseTimestamp > 0 &&
273                _cliffAmount > 0 &&
274                _cliffReleaseTimestamp <= _startTimestamp
275            ) || (
276                _cliffReleaseTimestamp == 0 &&
277                _cliffAmount == 0
278        ), "INVALID_CLIFF");

295        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

344        require(_startTimestamps.length == length &&
345                _endTimestamps.length == length &&
346                _cliffReleaseTimestamps.length == length &&
347                _releaseIntervalsSecs.length == length &&
348                _linearVestAmounts.length == length &&
349                _cliffAmounts.length == length,
350                "ARRAY_LENGTH_MISMATCH"
351        );

374        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

402        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");

426        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

447        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor

449        require(bal > 0, "INSUFFICIENT_BALANCE");
```

## [G‑03] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)

Saves 5 gas per loop

```solidity
File: /contracts/VTVLVesting.sol

353        for (uint256 i = 0; i < length; i++) {
```

## [G‑04] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

```solidity
File: /contracts/VTVLVesting.sol

353        for (uint256 i = 0; i < length; i++) {
```

## [G-05] Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement

`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

43            mintableSupply -= amount;
```

```solidity
File: /contracts/VTVLVesting.sol

104                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start

107                uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval

167                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start

170                uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval

429        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
```

## [G-06] Overcheck if the account it is not `address(0)`

In the [mint of OpenZeppelin ERC20 implementation](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/e968b56df41db8ad080b734dd18b0d0274218be1/contracts/token/ERC20/ERC20.sol#L257-L267), it's checked if the account it is not `address(0)`

```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

37        require(account != address(0), "INVALID_ADDRESS");
```

## [G-07] The use of `_msgSender()` when there is no implementation of a meta transaction mechanism that uses it, such as EIP-2771, very slightly increases gas consumption

Also can remove the `Context.sol` of the heredity and his import

```solidity
File: /contracts/token/FullPremintERC20Token.sol

12        _mint(_msgSender(), supply_);
```

```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

32            mint(_msgSender(), initialSupply_);
```

```solidity
File: /contracts/AccessProtected.sol

17        _admins[_msgSender()] = true;

18        emit AdminAccessSet(_msgSender(), true);

25        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
```

```solidity
File: /contracts/VTVLVesting.sol

364    function withdraw() hasActiveClaim(_msgSender()) external {

367        Claim storage usrClaim = claims[_msgSender()];

371        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));

388        tokenAddress.safeTransfer(_msgSender(), amountRemaining);

391        emit Claimed(_msgSender(), amountRemaining);

407        tokenAddress.safeTransfer(_msgSender(), _amountRequested);

410        emit AdminWithdrawn(_msgSender(), _amountRequested);

450        _otherTokenAddress.safeTransfer(_msgSender(), bal);
```

## [G-08] `public` functions to `external` functions

The following functions could be set external to save gas and improve code quality
`external` call cost is less expensive than of `public` functions

```solidity
File: /contracts/AccessProtected.sol

39    function setAdmin(address admin, bool isEnabled) public onlyAdmin {
```

```solidity
File: /contracts/VTVLVesting.sol

398    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
```

## [G-09] Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead.

If the array is passed to an `internal` function which passes the array to another `internal` function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the `internal` functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

```solidity
File: /contracts/VTVLVesting.sol

334        address[] memory _recipients,
335        uint40[] memory _startTimestamps,
336        uint40[] memory _endTimestamps,
337        uint40[] memory _cliffReleaseTimestamps,
```

## [G-10] Use `ClaimCreated` event to track the recipients of the vesting instead of storage in `vestingRecipients` array

The `vestingRecipients` array it's not used onchain, also the vesting recipient is emited by the `ClaimCreated`, with this event can track history recipients and his claim amount
Remove the `vestingRecipients` and his getters and setter

```solidity
File: /contracts/VTVLVesting.sol

 52    // Track the recipients of the vesting
 53    address[] internal vestingRecipients;

220    /**
221    @notice Return all the addresses that have vesting schedules attached.
222    */
223    function allVestingRecipients() external view returns (address[] memory) {
224        return vestingRecipients;
225    }

227    /**
228    @notice Get the total number of vesting recipients.
229    */
220    function numVestingRecipients() external view returns (uint256) {
231        return vestingRecipients.length;
232    }

302        vestingRecipients.push(_recipient); // add the vesting recipient to the list
```

## [G-11] Redundant type casting

The `ClaimRevoked` event use `uint256` type to `revocationTimestamp` parameter

```solidity
File: /contracts/VTVLVesting.sol

436        emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);
```

## [G-12] Redundant check

```solidity
File: /contracts/VTVLVesting.sol

426        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
Checked in:
187        return (vestAmt > _claim.amountWithdrawn) ? vestAmt : _claim.amountWithdrawn;
```

## [G-13] Overcalculate finalVestedAmount

In [`revokeClaim`](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L418-L437) can direct calculate the [`finalVestAmt`](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L422) like in [L292](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L292)
And `finalVestedAmount` function could be marked as `external` instead of `public`

```solidity
File: /contracts/VTVLVesting.sol

From:
422        uint112 finalVestAmt = finalVestedAmount(_recipient);
To:
422        uint112 finalVestAmt = _claim.cliffAmount + _claim.linearVestAmount;`
```