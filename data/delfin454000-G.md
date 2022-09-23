### Avoid use of '&&' within a `require` function
Splitting into separate `require()` statements saves gas
[VTVLVesting.sol: L344-351](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L351)
```solidity
        require(_startTimestamps.length == length &&
                _endTimestamps.length == length &&
                _cliffReleaseTimestamps.length == length &&
                _releaseIntervalsSecs.length == length &&
                _linearVestAmounts.length == length &&
                _cliffAmounts.length == length, 
                "ARRAY_LENGTH_MISMATCH"
        );
```
Recommendation:
```solidity
        require(_startTimestamps.length == length, "ARRAY_LENGTH_MISMATCH";
        require(_endTimestamps.length == length, "ARRAY_LENGTH_MISMATCH";
        require(_cliffReleaseTimestamps.length == length, "ARRAY_LENGTH_MISMATCH";
        require(_releaseIntervalsSecs.length == length, "ARRAY_LENGTH_MISMATCH";
        require(_linearVestAmounts.length == length, "ARRAY_LENGTH_MISMATCH";
        require(_cliffAmounts.length == length, "ARRAY_LENGTH_MISMATCH";
```
___
___

### Use `!= 0` instead of `> 0` in a `require` statement if variable is an unsigned integer (`uint`) 
`!= 0` should be used where possible since `> 0` costs more gas

[FullPremintERC20Token.sol: L8-14](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L8-L14)
```solidity
contract FullPremintERC20Token is ERC20 {
    // uint constant _initialSupply = 100 * (10**18);
    constructor(string memory name_, string memory symbol_, uint256 supply_) ERC20(name_, symbol_) {
        require(supply_ > 0, "NO_ZERO_MINT");
        _mint(_msgSender(), supply_);
    }
}
```
Recommendation: Change `supply_ > 0` to `supply != 0`
___
___

### State variables should not be initialized to their default values
Initializing `uint` variables to their default value of `0` is unnecessary and costs gas
___
[VTVLVesting.sol: L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27)
```solidity
    uint112 public numTokensReservedForVesting = 0;
```
Change to: 
```solidity
    uint112 public numTokensReservedForVesting;
```
___
[VTVLVesting.sol: L148](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148)
```solidity
        uint112 vestAmt = 0;
```
Change to: 
```solidity
        uint112 vestAmt;
```
___
___

### Use `++i` instead of `i++` to increase count in a `for` loop
Since use of  `i++` (or equivalent counter) costs more gas, it is better to use `++i`
___
[VTVLVesting.sol: L353-355](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353-L355)
```solidity
        for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```
Suggestion:
```solidity
        for (uint256 i = 0; i < length; ++i) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```
___
___


### Inline a function/modifier that’s only used once
It costs less gas to inline the code of functions that are only called once. Doing so saves the cost of allocating the function selector, and the cost of the jump when the function is called.
___
[VTVLVesting.sol: L123-140](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L123-L140)
```solidity
    modifier hasNoClaim(address _recipient) {
        Claim storage _claim = claims[_recipient];
        // Start timestamp != 0 is a sufficient condition for a claim to exist
        // This is because we only ever add claims (or modify startTs) in the createClaim function 
        // Which requires that its input startTimestamp be nonzero
        // So therefore, a zero value for this indicates the claim does not exist.
        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
        
        // We don't even need to check for active to be unset, since this function only 
        // determines that a claim hasn't been set
        // require(_claim.isActive == false, "CLAIM_ALREADY_EXISTS");
    
        // Further checks aren't necessary (to save gas), as they're done at creation time (createClaim)
        // require(_claim.endTimestamp == 0, "CLAIM_ALREADY_EXISTS");
        // require(_claim.linearVestAmount + _claim.cliffAmount == 0, "CLAIM_ALREADY_EXISTS");
        // require(_claim.amountWithdrawn == 0, "CLAIM_ALREADY_EXISTS");
        _;
    }
```
Since `hasNoClaim()` is used only once in this contract (in `function _createClaimUnchecked()`) as shown below, it should be inlined to save gas

[VTVLVesting.sol: L245-253](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L245-L253)
```solidity
    function _createClaimUnchecked(
            address _recipient, 
            uint40 _startTimestamp, 
            uint40 _endTimestamp, 
            uint40 _cliffReleaseTimestamp, 
            uint40 _releaseIntervalSecs, 
            uint112 _linearVestAmount, 
            uint112 _cliffAmount
                ) private  hasNoClaim(_recipient) {
```
___
___