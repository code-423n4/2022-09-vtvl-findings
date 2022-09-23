Total Gas Optimizations: 10

## (1) Using Calldata Instead Of Memory For Read-only Arguments In External Functions Saves Gas

Severity: Gas Optimizations

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one


## Proof Of Concept


	function allVestingRecipients() external view returns (address[] memory) {
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L223

	function createClaimsBatch(
        address[] memory _recipients, 
        uint40[] memory _startTimestamps, 
        uint40[] memory _endTimestamps, 
        uint40[] memory _cliffReleaseTimestamps, 
        uint40[] memory _releaseIntervalsSecs, 
        uint112[] memory _linearVestAmounts, 
        uint112[] memory _cliffAmounts) 
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L333-L340






## (2) ++i Costs Less Gas Than i++, Especially When It’s Used In For-loops (--i/i-- Too)

Severity: Gas Optimizations

Saves 6 gas per loop

## Proof Of Concept


	for (uint256 i = 0; i < length; i++) {
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L353



## Recommended Mitigation Steps

For example, use ++i instead of i++



## (3) It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied

Severity: Gas Optimizations

## Proof Of Concept


	for (uint256 i = 0; i < length; i++) {
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L353






## (4) Using > 0 Costs More Gas Than != 0 When Used On A Uint In A Require() Statement

Severity: Gas Optimizations

This change saves 6 gas per instance

## Proof Of Concept


	require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L256

	require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L257

	require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L263

	require(bal > 0, "INSUFFICIENT_BALANCE");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L449



## (5) <x> -=/+= <y> Costs More Gas Than <x> = <x> -/+ <y> For State Variables

Severity: Gas Optimizations

## Proof Of Concept


	vestAmt += _claim.cliffAmount;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L161

	vestAmt += linearVestAmount;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L179

	numTokensReservedForVesting += allocatedAmount;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L301

	usrClaim.amountWithdrawn += amountRemaining;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L381

	numTokensReservedForVesting -= amountRemaining;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L383

	numTokensReservedForVesting -= amountRemaining; // Reduces the allocation
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L433

	mintableSupply -= amount;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L43





## (6) Public Functions To External

Severity: Gas Optimizations

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

## Proof Of Concept


	function setAdmin(address admin, bool isEnabled) public onlyAdmin {
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L39

	function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L398

	function mint(address account, uint256 amount) public onlyAdmin {
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L36



## (7) Use of non-uint64 state variable is less efficient than uint256

Severity: Gas Optimizations

Lower than uint256 size storage variables are less gas efficient.
Using uint64 does not give any efficiency, actually, it is the opposite as EVM operates on default of 256-bit values so uint64 is more expensive in this case as it needs a conversion. It only gives improvements in cases where you can pack variables together, e.g. structs.

## Proof Of Concept


	uint112 public numTokensReservedForVesting = 0;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L27

	uint40 startTimestamp;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L35

	uint40 endTimestamp;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L36

	uint40 cliffReleaseTimestamp;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L37

	uint40 releaseIntervalSecs;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L38

	uint112 linearVestAmount;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L42

	uint112 cliffAmount;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L43

	uint112 amountWithdrawn;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L44

	uint112 vestAmt = 0;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L148

	uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L167

	uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L169

	uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L170

	uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L176

	uint112 allocatedAmount = _cliffAmount + _linearVestAmount;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L292

	uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L371

	uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L377

	uint112 finalVestAmt = finalVestedAmount(_recipient);
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L422

	uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L429






## (8) ++i/i++ Should Be Unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

Severity: Gas Optimizations

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

## Proof Of Concept


	for (uint256 i = 0; i < length; i++) {
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L353




## (9) Using Bools For Storage Incurs Overhead

Severity: Gas Optimizations

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

## Proof Of Concept


    mapping(address => bool) private _admins;
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L12


## (10) No need to check for true in require

Severity: Gas Optimizations

Since isActive is bool, there is no need to check for "true" value, can simply call:
require(_claim.isActive, "NO_ACTIVE_CLAIM");

## Proof Of Concept

	require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L111

## Recommended Mitigation Steps

	require(_claim.isActive, "NO_ACTIVE_CLAIM");
