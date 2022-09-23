# 1. UNCHECKED{++I} INSTEAD OF I++ (OR USE ASSEMBLY WHEN APPLICABLE)
### Description
Use `++i` instead of `i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. You can also use `unchecked{++i;}` for even more gas savings but this will not check to see if `i` overflows. For extra safety if you are worried about this, you can add a require statement after the loop checking if `i` is equal to the final incremented value. For best gas savings, use inline assembly, however this limits the functionality you can achieve. For example you cant use Solidity syntax to internally call your own contract within an assembly block and external calls must be done with the `call()` or `delegatecall()` instruction. However when applicable, inline assembly will save much more gas.
### Links to github files
[VTVLVesting.sol:L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)
### Instances
```
contracts/VTVLVesting.sol:353:        for (uint256 i = 0; i < length; i++) {
```
----
# 2. Using > 0 costs more gas than != 0 when used on a uint in a require() statement
### Description
0 is less efficient than != 0 for unsigned integers (with proof)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas) Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proof: https://twitter.com/gzeon/status/1485428085885640706

### Links to github files
[VTVLVesting.sol:L107](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107)
[VTVLVesting.sol:L256](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256)
[VTVLVesting.sol:L257](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L257)
[VTVLVesting.sol:L263](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263)
[VTVLVesting.sol:L449](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449)
[FullPremintERC20Token.sol:L11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11)
[VariableSupplyERC20Token.sol:L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27)
### Instances
```
contracts/VTVLVesting.sol:107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
contracts/VTVLVesting.sol:256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
contracts/VTVLVesting.sol:257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
contracts/VTVLVesting.sol:263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
contracts/VTVLVesting.sol:449:        require(bal > 0, "INSUFFICIENT_BALANCE");
contracts/token/FullPremintERC20Token.sol:11:        require(supply_ > 0, "NO_ZERO_MINT");
contracts/token/VariableSupplyERC20Token.sol:27:        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
```

### Recommended Mitigation Steps:
I suggest changing > 0 with != 0. Also, please enable the Optimizer.

----
# 3. Public functions not called by the contract should be declared external instead
### Description
public functions not called by the contract should be declared external instead to save some gas cost.
### Links to github files
[VTVLVesting.sol:L398](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398)
[AccessProtected.sol:L39](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39)
### Instances
```
contracts/VTVLVesting.sol:398:    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    
contracts/AccessProtected.sol:39:    function setAdmin(address admin, bool isEnabled) public onlyAdmin {
```
### Recommended Mitigation Steps:
declare functions as external instead of public

----
# 4. Splitting require() statements that use && saves gas
### Description
Require statements including conditions with the && operator can be broken down in multiple require statements to save gas.
### Links to github file
[VTVLVesting.sol:L344-L351](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L351)
### Instance
```
require(_startTimestamps.length == length &&
        _endTimestamps.length == length &&
        _cliffReleaseTimestamps.length == length &&
        _releaseIntervalsSecs.length == length &&
        _linearVestAmounts.length == length &&
        _cliffAmounts.length == length, 
        "ARRAY_LENGTH_MISMATCH"
);
```
### Recommended Mitigation Steps:
Breakdown each condition in a separate require statement (though require statements should be replaced with custom errors)

----
# 5. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops
### Description
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)
### Links to github files
[VTVLVesting.sol:L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)
### Instances
```
contracts/VTVLVesting.sol:353:        for (uint256 i = 0; i < length; i++) {
```
----
# 6. Custom Errors instead of Revert Strings to save Gas
### Description
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
### Links to github files
[VTVLVesting.sol:L82](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L82)
[VTVLVesting.sol:L107](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107)
[VTVLVesting.sol:L111](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111)
[VTVLVesting.sol:L129](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L129)
[VTVLVesting.sol:L255](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255)
[VTVLVesting.sol:L256](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256)
[VTVLVesting.sol:L257](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L257)
[VTVLVesting.sol:L262](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L262)
[VTVLVesting.sol:L263](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263)
[VTVLVesting.sol:L264](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L264)
[VTVLVesting.sol:L270](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270)
[VTVLVesting.sol:L295](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L295)
[VTVLVesting.sol:L344](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344)
[VTVLVesting.sol:L374](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374)
[VTVLVesting.sol:L402](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L402)
[VTVLVesting.sol:L426](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L426)
[VTVLVesting.sol:L447](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L447)
[VTVLVesting.sol:L449](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449)
[AccessProtected.sol:L25](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L25)
[AccessProtected.sol:L40](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L40)
[FullPremintERC20Token.sol:L11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11)
[VariableSupplyERC20Token.sol:L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27)
[VariableSupplyERC20Token.sol:L37](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L37)
[VariableSupplyERC20Token.sol:L41](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41)
### Instances
```
contracts/VTVLVesting.sol:82:        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");
contracts/VTVLVesting.sol:107:        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");
contracts/VTVLVesting.sol:111:        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
contracts/VTVLVesting.sol:129:        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");
contracts/VTVLVesting.sol:255:        require(_recipient != address(0), "INVALID_ADDRESS");
contracts/VTVLVesting.sol:256:        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both
contracts/VTVLVesting.sol:257:        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
contracts/VTVLVesting.sol:262:        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp
contracts/VTVLVesting.sol:263:        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
contracts/VTVLVesting.sol:264:        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");
contracts/VTVLVesting.sol:270:        require( 
contracts/VTVLVesting.sol:295:        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");
contracts/VTVLVesting.sol:344:        require(_startTimestamps.length == length &&
contracts/VTVLVesting.sol:374:        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");
contracts/VTVLVesting.sol:402:        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");
contracts/VTVLVesting.sol:426:        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
contracts/VTVLVesting.sol:447:        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
contracts/VTVLVesting.sol:449:        require(bal > 0, "INSUFFICIENT_BALANCE");
contracts/AccessProtected.sol:25:        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");
contracts/AccessProtected.sol:40:        require(admin != address(0), "INVALID_ADDRESS");
contracts/token/FullPremintERC20Token.sol:11:        require(supply_ > 0, "NO_ZERO_MINT");
contracts/token/VariableSupplyERC20Token.sol:27:        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
contracts/token/VariableSupplyERC20Token.sol:37:        require(account != address(0), "INVALID_ADDRESS");
contracts/token/VariableSupplyERC20Token.sol:41:            require(amount <= mintableSupply, "INVALID_AMOUNT");
```
### Recommended Mitigation Steps:
I suggest replacing revert strings with custom errors.

----
# 7. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
`<x> += <y>` costs more gas as compared to `<x> = <x> + <y>`
### Links to github files
[VTVLVesting.sol:L161](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L161)
[VTVLVesting.sol:L179](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L179)
[VTVLVesting.sol:L301](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L301)
[VTVLVesting.sol:L381](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L381)
### Instances
```
contracts/VTVLVesting.sol:161:                vestAmt += _claim.cliffAmount;
contracts/VTVLVesting.sol:179:                vestAmt += linearVestAmount;
contracts/VTVLVesting.sol:301:        numTokensReservedForVesting += allocatedAmount; // track the allocated amount
contracts/VTVLVesting.sol:381:        usrClaim.amountWithdrawn += amountRemaining;
```
`<x> -= <y>` costs more gas as compared to `<x> = <x> - <y>`
### Links to github files
[VTVLVesting.sol:L383](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L383)
[VTVLVesting.sol:L433](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L433)
[VariableSupplyERC20Token.sol:L43](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L43)
### Instances
```
contracts/VTVLVesting.sol:383:        numTokensReservedForVesting -= amountRemaining;
contracts/VTVLVesting.sol:433:        numTokensReservedForVesting -= amountRemaining; // Reduces the allocation
contracts/token/VariableSupplyERC20Token.sol:43:            mintableSupply -= amount;
```

----
# 8. Variables: No need to explicitly initialize variables with default values
### Description
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint number;` instead of `uint number = 0;`
### Links to github files
[VTVLVesting.sol:L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27)
[VTVLVesting.sol:L148](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148)
### Instances
```
contracts/VTVLVesting.sol:27:    uint112 public numTokensReservedForVesting = 0;
contracts/VTVLVesting.sol:148:        uint112 vestAmt = 0;
```
### Recommendation:
I suggest removing explicit initializations for default values.

----
# 9. IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED
### Description
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for `(uint256 i = 0; i < numIterations; ++i)` { should be replaced with for `(uint256 i; i < numIterations; ++i) {`
### Links to github files
[VTVLVesting.sol:L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)
### Instances
```
contracts/VTVLVesting.sol:353:        for (uint256 i = 0; i < length; i++) {
```
----
# 10. Use a more recent version of solidity
### Description
Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value
### Links to github files
[VTVLVesting.sol:L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L2)
[AccessProtected.sol:L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L2)
[FullPremintERC20Token.sol:L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L2)
[VariableSupplyERC20Token.sol:L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L2)
### Instances
```
contracts/VTVLVesting.sol:2:pragma solidity 0.8.14;
contracts/AccessProtected.sol:2:pragma solidity 0.8.14;
contracts/token/FullPremintERC20Token.sol:2:pragma solidity 0.8.14;
contracts/token/VariableSupplyERC20Token.sol:2:pragma solidity ^0.8.14;
```
----
# 11. Inline a modifier that’s only used once
### Description
As hasNoClaim() is only used once in this contract (in function proxiableUUID()), it should get inlined to save gas:
### Links to github files
[VTVLVesting.sol:L123](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L123)
### Instances
```
contracts/VTVLVesting.sol:123:    modifier hasNoClaim(address _recipient) {
```
### Links to github file where modifiers are used only once
[VTVLVesting.sol:L245-L253](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L245-L253)
### Instances where modifiers are used only once
```
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
----
# 12. Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
### Description
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it’s still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one
### Links to github files
[VTVLVesting.sol:L333-L341](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L333-L341)
### Instances
```
function createClaimsBatch(
    address[] memory _recipients, 
    uint40[] memory _startTimestamps, 
    uint40[] memory _endTimestamps, 
    uint40[] memory _cliffReleaseTimestamps, 
    uint40[] memory _releaseIntervalsSecs, 
    uint112[] memory _linearVestAmounts, 
    uint112[] memory _cliffAmounts) 
    external onlyAdmin {
```
### Recommended Mitigation Steps
use `calldata` instead of `memory`

----
# 13. Using private rather than public for constants / immutable, saves gas
### Description
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves **3406-3606** gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table
### Links to github files
[VTVLVesting.sol:L17](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L17)
### Instances
```
contracts/VTVLVesting.sol:17:    IERC20 public immutable tokenAddress;
```
----