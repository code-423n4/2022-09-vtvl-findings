## **FullPremintERC20Token.sol**

1. The `FullPremintERC20Token` contract can be refactored by replacing the `require` statement inside the constructor with an `if` statement and a custom error:

```
contract FullPremintERC20Tokem is ERC20 {
    constructor(string memory name_, string memory symbol_, uint256 supply_) ERC20(name_, symbol_) {
        `require`(supply_ > 0, "NO_ZERO_MINT");
        _mint(_msgSender(), supply_);
    }
}
// GAS DEPLOYMENT COST => 1186340
```

As shown, the same contract saves almost 2,000 gas with this optimization:

```
contract FullPremintERC20Token is ERC20 {
    error NoZeroMint();

    constructor(string memory name_, string memory symbol_, uint256 supply_) ERC20(name_, symbol_) {
        if(supply_== 0) revert NoZeroMint();
        _mint(_msgSender(), supply_);
    }
}
// GAS DEPLOYMENT COST => 1184736
```

NOTE: The majority of the following optimizations refers to the exact same case of require-if statements.

## **AccessProtected.sol**

2. The `onlyAdmin()` function modifier can be refactored. The `require` statement can be replaced with an`ìf` statement and a custom error. Declare error:

`error AdminAccessRequired();`

Replace line 25 with:

```
if(!_admins[_msgSender()) revert AdminAccessRequired();
```

3. The `setAdmin()` function can be refactored. The `require` statement can be replaced with an`ìf` statement and a custom error. Declare error:

`error InvalidAddress();`

Replace line 25 with:

```
if(admin == address(0)) revert InvalidAddress();
```

NOTE: As it will be demonstrated on following optimizations, the error `InvalidAddress()` can be used on `VariableSupplyERC20Token` and `VariableSupplyERC20Token` and both of these contracts inherit from `AccessProtected` contract. This makes the optimization greater because the error is declared just once and used in three contracts..

## **VariableSupplyERC20Token.sol**

4. The `require` statement on line 27 can be refactored by replacing the `require` statement with an `if` statement and a custom error. Declare error:

   `error InvalidAmount();`

   and replace line 27 with:

   `if (initialSupply_ == 0 || maxSupply_ == 0) revert InvalidAmount();`

5. The `require` statement on line 37 can be refactored by replacing the `require` statement with an `if` statement and a custom error. Use `InvalidAddress();` error inherited from `AccessProtected.sol` and replace line 37 with:

   `if (account == address(0)) revert InvalidAddress();`

6. The `require` statement on line 41 can be refactored by replacing the `require` statement with an `if` statement and a custom error. Use previoulsy declared error and replace line 41 with:

   `if (amount > mintableSupply) revert InvalidAmount();`

## **VTVLVesting.sol**

The `require` statements for this contract can be refactored. Declare the following errors:

```
error NoActiveClaim();
error ClaimAlreadyExists();
error InvalidVestedAmount();
error InvalidStartTimestamp();
error InvalidEndTimestamp();
error InvalidReleaseInterval();
error InvalidIntervalLength();
error InvalidCliff();
error InsufficientBalance();
error ArrayLengthMismatch();
error NothingToWithdraw();
error NoUnvestedAmount();
```

7. The `constructor` on line 81 has a `require` statement that can be refactored like so:

```
constructor(IERC20 _tokenAddress) {
	if(address(_tokenAddress) == address(0)) revert InvalidAddress();
	tokenAddress = _tokenAddress;
}
```

8. The `hasActiveClaim` modifier on line 105 has two `require` statements that can be refactored like so:

```
modifier hasActiveClaim(address _recipient) {
	Claim storage _claim = claims[_recipient];
	if(_claim.startTimestamp == 0) revert NoActiveClaim();
	if(!_claim.isActive) revert NoActiveClaim();
	_;
}
```

9. The `hasNoClaim` modifier on line 123 has a `require` statement that can be refactored like so:

```
modifier hasNoClaim(address _recipient) {
	Claim storage _claim = claims[_recipient];
	if(_claim.startTimestamp > 0) revert ClaimAlreadyExists();
	_;
}
```

10. The `_createClaimUnchecked()` function on line 245 has eight `require` statements can be refactored like so:

```
function _createClaimUnchecked(...) private  hasNoClaim(_recipient) {
	if(_recipient == address(0)) revert InvalidAddress();
	if(_linearVestAmount+_cliffAmount == 0) revert InvalidVestedAmount();
	if(_startTimestamp == 0) revert InvalidStartTimestamp();
	if(_startTimestamp >= _endTimestamp) revert InvalidEndTimestamp();
	if(_releaseIntervalSecs == 0) revert InvalidReleaseInterval();
	if(!((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0)) revert InvalidIntervalLength();

		if (
			(_cliffReleaseTimestamp == 0 || _cliffAmount == 0 || _cliffReleaseTimestamp > _startTimestamp) ||
			(_cliffReleaseTimestamp > 0 || _cliffAmount > 0)
		) revert InvalidCliff();

	Claim memory _claim = Claim({
		...
	});
	uint112 allocatedAmount = _cliffAmount + _linearVestAmount;

	if(tokenAddress.balanceOf(address(this)) < numTokensReservedForVesting + allocatedAmount) revert InsufficientBalance();

	claims[_recipient] = _claim; // store the claim
	numTokensReservedForVesting += allocatedAmount; // track the allocated amount
	vestingRecipients.push(_recipient); // add the vesting recipient to the list
	emit ClaimCreated(_recipient, _claim); // let everyone know
}

```

11. The `createClaimsBatch()` function on line 333 has a `require` statement that can be refactored:

```
uint256 length = _recipients.length;

if(
	startTimestamps.length != length ||
	endTimestamps.length != length ||
	cliffReleaseTimestamps.length != length ||
	releaseIntervalsSecs.length != length ||
	linearVestAmounts.length != length ||
	cliffAmounts.length != length
) revert ArrayLengthMismatch();
```

12. The `withdraw()` function on line 364 has a `require` statement that can be refactored:

```
if (allowance <= usrClaim.amountWithdrawn) revert NothingToWithdraw();
```

13. The `withdrawAdmin()` function on line 398 has a `require` statement that can be refactored:

```
if(amountRemaining < _amountRequested) revert InsufficientBalance();
```

14. The `revokeClaim()` function on line 418 has a `require` statement that can be refactored:

```
if(_claim.amountWithdrawn >= finalVestAmt) revert NoUnvestedAmount();
```

15. The `withdrawOtherToken()` function on line 446 has a `require` statement that can be refactored:

```
if(_otherTokenAddress == tokenAddress) revert InvalidAddress();
uint256 bal = _otherTokenAddress.balanceOf(address(this));
if(bal ==0) revert InsufficientBalance();
```

NOTE: As you can see some custom errors can be re-used saving even more gas compared to the reason `string` given inside `require` statements, that are re-written every time.

16. The `for` loop inside the `createClaimsBatch()` function uses post-increment to the variable `i`, like so:

`for (uint256 i = 0; i < length; i++) {...}`

- Replace the post-increment with pre-increment to save 6 gas for each `i` in the loop.

`for (uint256 i = 0; i < length; ++i) {...}`
