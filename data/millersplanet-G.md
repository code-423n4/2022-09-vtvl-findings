# **Gas Optimizations**

## **FullPremintERC20Token.sol**

1. The `FullPremintERC20Token` contract can be refactored for gas optimizations:

```
contract FullPremintERC20Tokem is ERC20 {
    constructor(string memory name_, string memory symbol_, uint256 supply_) ERC20(name_, symbol_) {
        require(supply_ > 0, "NO_ZERO_MINT");
        _mint(_msgSender(), supply_);
    }
}
// GAS DEPLOYMENT COST => 1186340
```

This is a more effective way of declaring custom errors while saving some gas. Here the same contract sames almost 2,000 gas:

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

## **VariableSupplyERC20Token.sol**

2. The following require statement on line 27 can be refactored in a way that saves gas using custom errors. Declare error:

   `error InvalidAmount();`

   and replace line 27 with:

   `if (initialSupply_ == 0 || maxSupply_ == 0) revert InvalidAmount();`

3. The following require statement on line 37 can be refactored in a way that saves gas using custom errors. Declare error:

   `error InvalidAdddress();`

   and replace line 37 with:

   `if (account == address(0)) revert InvalidAddress();`

4. The following require statement on line 37 can be refactored in a way that saves gas using custom errors. Replace line 41 with:

   `if (amount > mintableSupply) revert InvalidAmount();`

   NOTE: As you can see here the contracts reuses an existing custom error instead of re-declaring a reason `string` inside a `require` statement.

## **VTVLVesting.sol**

The require statements for this contract can be refactored. Declare the following errors:

```
error NoActiveClaim();
error ClaimAlreadyExists();
error InvalidAddress();
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

5. The `constructor` can be refactored for gas optimizations:

```
constructor(IERC20 _tokenAddress) {
	if(_tokenAddress == 0) revert InvalidAddress();
	tokenAddress = _tokenAddress;
}
```

6. The `hasActiveClaim` modifier can be refactored for gas optimizations:

```
modifier hasActiveClaim(address _recipient) {
	Claim storage _claim = claims[_recipient];
	if(_claim.startTimestamp == 0) revert NoActiveClaim();
	if(!_claim.isActive) revert NoActiveClaim();
	_;
}
```

7. The `hasNoClaim` modifier can be refactored for gas optimizations:

```
modifier hasNoClaim(address _recipient) {
	Claim storage _claim = claims[_recipient];
	if(_claim.startTimestamp > 0) revert ClaimAlreadyExists();
	_;
}
```

8. The `_createClaimUnchecked()` function requirements can be refactored for gas optimizations:

```
function _createClaimUnchecked(...) private  hasNoClaim(_recipient) {
	if(_recipient == address(0)) revert InvalidAddress();
	if(_linearVestAmount+_cliffAmount == 0) revert InvalidVestedAmount();
	if(_startTimestamp == 0) revert InvalidStartTimestamp();
	if(_startTimestamp >= _endTimestamp) revert InvalidEndTimestamp();
	if(_releaseIntervalSecs == 0) revert InvalidReleaseInterval();
	if(!((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0)) revert InvalidIntervalLength();
	if( _cliffReleaseTimestamp == 0 || _cliffAmount == 0 || cliffReleaseTimestamp > _startTimestamp) revert InvalidCliff();
	if(_cliffReleaseTimestamp > 0 || _cliffAmount > 0) revert InvalidCliff();

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

9. The `createClaimsBatch()` function requirement can be refactored for gas optimizations:

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

10. The `withdraw()` function requirement can be refactored for gas optimizations:

```
if (allowance <= usrClaim.amountWithdrawn) revert NothingToWithdraw();
```

11. The `withdrawAdmin()` function requirement can be refactored for gas optimizations:

```
if(amountRemaining < _amountRequested) revert InsufficientBalance();
```

12. The `revokeClaim()` function requirement can be refactored for gas optimizations:

```
if(_claim.amountWithdrawn >= finalVestAmt) revert NoUnvestedAmount();
```

13. The `withdrawOtherToken()` function requirement can be refactored for gas optimizations:

```
if(_otherTokenAddress == tokenAddress) revert InvalidAddress();
uint256 bal = _otherTokenAddress.balanceOf(address(this));
if(bal ==0) revert InsufficientBalance();
```

NOTE: As you can see some custom errors can be re-used saving even more gas compared to the reason `string` given inside `require` statements, that are re-written every time.
