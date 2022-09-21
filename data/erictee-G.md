### [G-01] Using bools for storage incurs overhead.


#### Impact
 
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use ```uint256(1)``` and ```uint256(2)``` for true/false to avoid a Gwarmaccess ([100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past


#### Findings:
```
contracts/AccessProtected.sol:L12    mapping(address => bool) private _admins; // user address => admin? mapping

```
### [G-02] Use custom errors rather than ```revert()```/```require()``` string to save gas


#### Impact
Custom errors are available from solidity version 0.8.4, it saves around 50 gas each time they are hit by avoiding having to allocate and store the revert string.


#### Findings:
```
contracts/VTVLVesting.sol:L82        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");

contracts/VTVLVesting.sol:L107        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

contracts/VTVLVesting.sol:L111        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

contracts/VTVLVesting.sol:L129        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");

contracts/VTVLVesting.sol:L255        require(_recipient != address(0), "INVALID_ADDRESS");

contracts/VTVLVesting.sol:L256        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both

contracts/VTVLVesting.sol:L257        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");


contracts/VTVLVesting.sol:L262        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp

contracts/VTVLVesting.sol:L263        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

contracts/VTVLVesting.sol:L264        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

contracts/VTVLVesting.sol:L295        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

contracts/VTVLVesting.sol:L374        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");

contracts/VTVLVesting.sol:L402        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");

contracts/VTVLVesting.sol:L426        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

contracts/VTVLVesting.sol:L447        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor

contracts/VTVLVesting.sol:L449        require(bal > 0, "INSUFFICIENT_BALANCE");

contracts/AccessProtected.sol:L25        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");

contracts/AccessProtected.sol:L40        require(admin != address(0), "INVALID_ADDRESS");

contracts/token/VariableSupplyERC20Token.sol:L27        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

contracts/token/VariableSupplyERC20Token.sol:L37        require(account != address(0), "INVALID_ADDRESS");

contracts/token/VariableSupplyERC20Token.sol:L41            require(amount <= mintableSupply, "INVALID_AMOUNT");

contracts/token/FullPremintERC20Token.sol:L11        require(supply_ > 0, "NO_ZERO_MINT");

```
### [G-03] Public functions not called by the contract should be declared external instead


#### Impact
public functions that are never called by the contract should be declared external to save gas.


#### Findings:
```
contracts/VTVLVesting.sol:L398    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    

contracts/AccessProtected.sol:L39    function setAdmin(address admin, bool isEnabled) public onlyAdmin {

```
### [G-04] Using ```> 0``` costs more gas than ```!= 0``` when used on a uint in a ```require()``` statement.


#### Impact
When working with unsigned integer types, comparisons with != 0 are cheaper than with > 0 . This changes saves 6 gas per instance.


#### Findings:
```
contracts/VTVLVesting.sol:L107        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

contracts/VTVLVesting.sol:L256        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both

contracts/VTVLVesting.sol:L257        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

contracts/VTVLVesting.sol:L263        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

contracts/VTVLVesting.sol:L449        require(bal > 0, "INSUFFICIENT_BALANCE");

contracts/token/VariableSupplyERC20Token.sol:L27        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

contracts/token/FullPremintERC20Token.sol:L11        require(supply_ > 0, "NO_ZERO_MINT");

```
### [G-05] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
contracts/VTVLVesting.sol:L398    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    

contracts/VTVLVesting.sol:L418    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {

contracts/VTVLVesting.sol:L446    function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin {

contracts/AccessProtected.sol:L39    function setAdmin(address admin, bool isEnabled) public onlyAdmin {

contracts/token/VariableSupplyERC20Token.sol:L36    function mint(address account, uint256 amount) public onlyAdmin {

```
### [G-06] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
contracts/VTVLVesting.sol:L161                vestAmt += _claim.cliffAmount;

contracts/VTVLVesting.sol:L179                vestAmt += linearVestAmount;

contracts/VTVLVesting.sol:L301        numTokensReservedForVesting += allocatedAmount; // track the allocated amount

contracts/VTVLVesting.sol:L381        usrClaim.amountWithdrawn += amountRemaining;

```
### [G-07] ++i costs less gas than i++, especially when it's used in for loops.


#### Impact
Saves 6 gas per loop.


#### Findings:
```
contracts/VTVLVesting.sol:L353        for (uint256 i = 0; i < length; i++) {

```

### [G-08] Splitting ```require()``` statements that use && saves gas.


#### Impact
Consider splitting the ```require()``` statements to save gas.


#### Findings:
```
contracts/VTVLVesting.sol:L270        require( 

contracts/VTVLVesting.sol:L344        require(_startTimestamps.length == length &&

```
### [G-09] Explicit initialization with zero not required


#### Impact
Explicit initialization with zero is not required for variable declaration because uints are 0 by default. Removing this will reduce contract size and save a bit of gas.


#### Findings:
```
contracts/VTVLVesting.sol:L27    uint112 public numTokensReservedForVesting = 0;

contracts/VTVLVesting.sol:L148        uint112 vestAmt = 0;

contracts/VTVLVesting.sol:L353        for (uint256 i = 0; i < length; i++) {

```
### [G-10] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
contracts/VTVLVesting.sol:L353        for (uint256 i = 0; i < length; i++) {

```
