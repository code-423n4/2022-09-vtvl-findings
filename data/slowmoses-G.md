## (1) Use Pre Increment Instead of Post Increment

Pre-increment uses a little less gas.

***

for (uint256 i = 0; i < length; i++) {
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L353

## (2) Use Custom Errors Instead of Strings for Revert and Require

Require strings and revert strings use about 50 more gas than custom errors.

***

require(supply_ > 0, "NO_ZERO_MINT");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/FullPremintERC20Token.sol#L11

require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L27

require(account != address(0), "INVALID_ADDRESS");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L37

require(amount <= mintableSupply, "INVALID_AMOUNT");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L41

require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L25

require(admin != address(0), "INVALID_ADDRESS");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L40

require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L82

require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L107

require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L111

// require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L114

// require(_claim.endTimestamp > 0, "NO_END_TIMESTAMP");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L115

require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L129

// require(_claim.isActive == false, "CLAIM_ALREADY_EXISTS");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L133

// require(_claim.endTimestamp == 0, "CLAIM_ALREADY_EXISTS");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L136

// require(_claim.linearVestAmount + _claim.cliffAmount == 0, "CLAIM_ALREADY_EXISTS");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L137

// require(_claim.amountWithdrawn == 0, "CLAIM_ALREADY_EXISTS");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L138

require(_recipient != address(0), "INVALID_ADDRESS");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L255

require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L256

require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L257

// require(_endTimestamp > 0, "_endTimestamp must be valid"); // not necessary because of the next condition (transitively)
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L261

require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L262

require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L263

require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L264

require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L295

require(_startTimestamps.length == length &&
        _endTimestamps.length == length &&
        _cliffReleaseTimestamps.length == length &&
        _releaseIntervalsSecs.length == length &&
        _linearVestAmounts.length == length &&
        _cliffAmounts.length == length,
        "ARRAY_LENGTH_MISMATCH"
);
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L344-L351

require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L374

require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L402

require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L426

require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L447

require(bal > 0, "INSUFFICIENT_BALANCE");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L449

## (3) Boolean Variables Should not be Used for Storage

Using UINT256(1) for true and UINT256(2) for false costs less gas than using boolean variables.

***

mapping(address => bool) private _admins; // user address => admin? mapping
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L12

## (4) Do not Initialize to Default Value

Non-const variables should not be initialized to their default value.
Just using the default costs less gas.
For example use: uint256 abc;
Instead of: uint256 abc=0;

***

uint112 public numTokensReservedForVesting = 0;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L27

uint112 vestAmt = 0;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L148

for (uint256 i = 0; i < length; i++) {
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L353

## (5) Require Statements with Multiple Checks Need More Gas than Multiple Require Statements.

See link for a detailed discussion.
https://github.com/code-423n4/2022-01-xdefi-findings/issues/128

***

require(
    (
        _cliffReleaseTimestamp > 0 &&
        _cliffAmount > 0 &&
        _cliffReleaseTimestamp <= _startTimestamp
    ) || (
        _cliffReleaseTimestamp == 0 &&
        _cliffAmount == 0
), "INVALID_CLIFF");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L270-L278

require(_startTimestamps.length == length &&
        _endTimestamps.length == length &&
        _cliffReleaseTimestamps.length == length &&
        _releaseIntervalsSecs.length == length &&
        _linearVestAmounts.length == length &&
        _cliffAmounts.length == length,
        "ARRAY_LENGTH_MISMATCH"
);
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L344-L351

## (6) Avoid Ints or Uints Smaller Than 256 Bits

Using ints or uints smaller than 256 bits can cost extra gas since the EVM operates with 32 bytes at a time.
Consider using int256 or uint256. You can downcast them when necessary.

***

uint40 startTimestamp; // When does the vesting start (40 bits is enough for TS)
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L35

uint40 endTimestamp; // When does the vesting end - the vesting goes linearly between the start and end timestamps
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L36

uint40 cliffReleaseTimestamp; // At which timestamp is the cliffAmount released. This must be <= startTimestamp
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L37

uint40 releaseIntervalSecs; // Every how many seconds does the vested amount increase.
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L38

uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L167

uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L169

uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L170

uint40 _startTimestamp,
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L247

uint40 _endTimestamp,
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L248

uint40 _cliffReleaseTimestamp,
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L249

uint40 _releaseIntervalSecs,
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L250

uint40 _startTimestamp,
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L319

uint40 _endTimestamp,
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L320

uint40 _cliffReleaseTimestamp,
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L321

uint40 _releaseIntervalSecs,
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L322

