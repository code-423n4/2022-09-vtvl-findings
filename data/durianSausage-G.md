### G01: custom error save more gas
#### problem
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information, as explained https://blog.soliditylang.org/2021/04/21/custom-errors/. 
Custom errors are defined using the error statement.
#### prof
AccessProtected.sol, 40, b'        require(admin != address(0), "INVALID_ADDRESS");'
token/FullPremintERC20Token.sol, 11, b'        require(supply_ > 0, "NO_ZERO_MINT");'
token/VariableSupplyERC20Token.sol, 27, b'        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");'
token/VariableSupplyERC20Token.sol, 37, b'        require(account != address(0), "INVALID_ADDRESS");'
token/VariableSupplyERC20Token.sol, 41, b'            require(amount <= mintableSupply, "INVALID_AMOUNT");'
VTVLVesting.sol, 82, b'        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");'
VTVLVesting.sol, 255, b'        require(_recipient != address(0), "INVALID_ADDRESS");'
VTVLVesting.sol, 256, b'        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both'
VTVLVesting.sol, 257, b'        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");'
VTVLVesting.sol, 262, b'        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp'
VTVLVesting.sol, 263, b'        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");'
VTVLVesting.sol, 264, b'        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");'
VTVLVesting.sol, 278, b'        require( \n            (\n                _cliffReleaseTimestamp > 0 && \n                _cliffAmount > 0 && \n                _cliffReleaseTimestamp <= _startTimestamp\n            ) || (\n                _cliffReleaseTimestamp == 0 && \n                _cliffAmount == 0\n        ), "INVALID_CLIFF");'
VTVLVesting.sol, 295, b'        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");'
VTVLVesting.sol, 351, b'        require(_startTimestamps.length == length &&\n                _endTimestamps.length == length &&\n                _cliffReleaseTimestamps.length == length &&\n                _releaseIntervalsSecs.length == length &&\n                _linearVestAmounts.length == length &&\n                _cliffAmounts.length == length, \n                "ARRAY_LENGTH_MISMATCH"\n        );'
VTVLVesting.sol, 374, b'        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");'
VTVLVesting.sol, 402, b'        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");'
VTVLVesting.sol, 426, b'        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");'
VTVLVesting.sol, 447, b'        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor'
VTVLVesting.sol, 449, b'        require(bal > 0, "INSUFFICIENT_BALANCE");'


### G02: COMPARISONS WITH ZERO FOR UNSIGNED INTEGERS
#### problem
0 is less gas efficient than !0 if you enable the optimizer at 10k AND you’re in a require statement. Detailed explanation with the opcodes https://twitter.com/gzeon/status/1485428085885640706
#### prof
token/FullPremintERC20Token.sol, 11, b'        require(supply_ > 0, "NO_ZERO_MINT");'
token/VariableSupplyERC20Token.sol, 27, b'        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");'
token/VariableSupplyERC20Token.sol, 27, b'        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");'
token/VariableSupplyERC20Token.sol, 31, b'        if(initialSupply_ > 0) {'
token/VariableSupplyERC20Token.sol, 40, b'        if(mintableSupply > 0) {'
VTVLVesting.sol, 256, b'        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both'
VTVLVesting.sol, 257, b'        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");'
VTVLVesting.sol, 263, b'        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");'
VTVLVesting.sol, 449, b'        require(bal > 0, "INSUFFICIENT_BALANCE");'

### G03: PREFIX INCREMENT SAVE MORE GAS
#### problem
prefix increment ++i is more cheaper than postfix i++
#### prof
VTVLVesting.sol, 353, b'        for (uint256 i = 0; i < length; i++) {'


### G04: X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES
#### prof
token/VariableSupplyERC20Token.sol, 43, b'            mintableSupply -= amount;'
VTVLVesting.sol, 301, b'        numTokensReservedForVesting += allocatedAmount; // track the allocated amount'
VTVLVesting.sol, 383, b'        numTokensReservedForVesting -= amountRemaining;'
VTVLVesting.sol, 433, b'        numTokensReservedForVesting -= amountRemaining; // Reduces the allocation'

### G05: USING BOOLS FOR STORAGE INCURS OVERHEAD
#### problem
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
#### prof
AccessProtected.sol, 12, b'    mapping(address => bool) private _admins; // user address => admin? mapping'


### G06: resign the default value to the variables.
#### problem
 resign the default value to the variables will cost more gas.
#### prof
VTVLVesting.sol, 148, b'        uint112 vestAmt = 0;'
VTVLVesting.sol, 353, b'        for (uint256 i = 0; i < length; i++) {'

### G07: ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
#### problem
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
#### prof
VTVLVesting.sol, 353, b'        for (uint256 i = 0; i < length; i++) {'

### G08: Splitting require() statements that use && saves gas
#### problem
See this issue(https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper
#### prof:
token/VariableSupplyERC20Token.sol, 27, b'        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");'
VTVLVesting.sol, 278, b'        require( \n            (\n                _cliffReleaseTimestamp > 0 && \n                _cliffAmount > 0 && \n                _cliffReleaseTimestamp <= _startTimestamp\n            ) || (\n                _cliffReleaseTimestamp == 0 && \n                _cliffAmount == 0\n        ), "INVALID_CLIFF");'
VTVLVesting.sol, 351, b'        require(_startTimestamps.length == length &&\n                _endTimestamps.length == length &&\n                _cliffReleaseTimestamps.length == length &&\n                _releaseIntervalsSecs.length == length &&\n                _linearVestAmounts.length == length &&\n                _cliffAmounts.length == length, \n                "ARRAY_LENGTH_MISMATCH"\n        );'


### G09: FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
#### problem
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
#### prof
AccessProtected.sol, 31, b'    function isAdmin(address _addressToCheck) external view returns (bool) '
AccessProtected.sol, 31, b'    function isAdmin(address _addressToCheck) external view returns (bool) '
AccessProtected.sol, 43, b'    function setAdmin(address admin, bool isEnabled) public onlyAdmin '
AccessProtected.sol, 43, b'    function setAdmin(address admin, bool isEnabled) public onlyAdmin '
token/VariableSupplyERC20Token.sol, 46, b'    function mint(address account, uint256 amount) public onlyAdmin '
token/VariableSupplyERC20Token.sol, 46, b'    function mint(address account, uint256 amount) public onlyAdmin '
VTVLVesting.sol, 327, b'    function createClaim(\n            address _recipient, \n            uint40 _startTimestamp, \n            uint40 _endTimestamp, \n            uint40 _cliffReleaseTimestamp, \n            uint40 _releaseIntervalSecs, \n            uint112 _linearVestAmount, \n            uint112 _cliffAmount\n                ) external onlyAdmin '
VTVLVesting.sol, 327, b'    function createClaim(\n            address _recipient, \n            uint40 _startTimestamp, \n            uint40 _endTimestamp, \n            uint40 _cliffReleaseTimestamp, \n            uint40 _releaseIntervalSecs, \n            uint112 _linearVestAmount, \n            uint112 _cliffAmount\n                ) external onlyAdmin '
VTVLVesting.sol, 358, b'    function createClaimsBatch(\n        address[] memory _recipients, \n        uint40[] memory _startTimestamps, \n        uint40[] memory _endTimestamps, \n        uint40[] memory _cliffReleaseTimestamps, \n        uint40[] memory _releaseIntervalsSecs, \n        uint112[] memory _linearVestAmounts, \n        uint112[] memory _cliffAmounts) \n        external onlyAdmin '
VTVLVesting.sol, 358, b'    function createClaimsBatch(\n        address[] memory _recipients, \n        uint40[] memory _startTimestamps, \n        uint40[] memory _endTimestamps, \n        uint40[] memory _cliffReleaseTimestamps, \n        uint40[] memory _releaseIntervalsSecs, \n        uint112[] memory _linearVestAmounts, \n        uint112[] memory _cliffAmounts) \n        external onlyAdmin '
VTVLVesting.sol, 411, b'    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin '
VTVLVesting.sol, 411, b'    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin '
VTVLVesting.sol, 437, b'    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) '
VTVLVesting.sol, 437, b'    function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) '
VTVLVesting.sol, 451, b'    function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin '
VTVLVesting.sol, 451, b'    function withdrawOtherToken(IERC20 _otherTokenAddress) external onlyAdmin '

### G10: USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
#### problem
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table
#### prof
VTVLVesting.sol, 17, b'    IERC20 public immutable tokenAddress;'

### G11: USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
#### problem
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
#### prof
AccessProtected.sol, 40, b'        require(admin != address(0), "INVALID_ADDRESS");'
token/FullPremintERC20Token.sol, 11, b'        require(supply_ > 0, "NO_ZERO_MINT");'
token/VariableSupplyERC20Token.sol, 27, b'        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");'
token/VariableSupplyERC20Token.sol, 27, b'        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");'
token/VariableSupplyERC20Token.sol, 31, b'        if(initialSupply_ > 0) {'
token/VariableSupplyERC20Token.sol, 37, b'        require(account != address(0), "INVALID_ADDRESS");'
token/VariableSupplyERC20Token.sol, 40, b'        if(mintableSupply > 0) {'
VTVLVesting.sol, 27, b'    uint112 public numTokensReservedForVesting = 0;'
VTVLVesting.sol, 82, b'        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");'
VTVLVesting.sol, 148, b'        uint112 vestAmt = 0;'
VTVLVesting.sol, 148, b'        uint112 vestAmt = 0;'
VTVLVesting.sol, 154, b'            if(_referenceTs > _claim.endTimestamp) {'
VTVLVesting.sol, 154, b'            if(_referenceTs > _claim.endTimestamp) {'
VTVLVesting.sol, 155, b'                _referenceTs = _claim.endTimestamp;'
VTVLVesting.sol, 160, b'            if(_referenceTs >= _claim.cliffReleaseTimestamp) {'
VTVLVesting.sol, 160, b'            if(_referenceTs >= _claim.cliffReleaseTimestamp) {'
VTVLVesting.sol, 161, b'                vestAmt += _claim.cliffAmount;'
VTVLVesting.sol, 166, b'            if(_referenceTs > _claim.startTimestamp) {'
VTVLVesting.sol, 166, b'            if(_referenceTs > _claim.startTimestamp) {'
VTVLVesting.sol, 167, b'                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start'
VTVLVesting.sol, 167, b'                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start'
VTVLVesting.sol, 167, b'                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start'
VTVLVesting.sol, 167, b'                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start'
VTVLVesting.sol, 169, b'                uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;'
VTVLVesting.sol, 169, b'                uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;'
VTVLVesting.sol, 169, b'                uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;'
VTVLVesting.sol, 169, b'                uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;'
VTVLVesting.sol, 170, b'                uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval'
VTVLVesting.sol, 170, b'                uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval'
VTVLVesting.sol, 170, b'                uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval'
VTVLVesting.sol, 170, b'                uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval'
VTVLVesting.sol, 176, b'                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;'
VTVLVesting.sol, 176, b'                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;'
VTVLVesting.sol, 176, b'                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;'
VTVLVesting.sol, 176, b'                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;'
VTVLVesting.sol, 176, b'                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;'
VTVLVesting.sol, 176, b'                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;'
VTVLVesting.sol, 179, b'                vestAmt += linearVestAmount;'
VTVLVesting.sol, 187, b'        return (vestAmt > _claim.amountWithdrawn) ? vestAmt : _claim.amountWithdrawn;'
VTVLVesting.sol, 198, b'        return _baseVestedAmount(_claim, _referenceTs);'
VTVLVesting.sol, 198, b'        return _baseVestedAmount(_claim, _referenceTs);'
VTVLVesting.sol, 198, b'        return _baseVestedAmount(_claim, _referenceTs);'
VTVLVesting.sol, 198, b'        return _baseVestedAmount(_claim, _referenceTs);'
VTVLVesting.sol, 208, b'        return _baseVestedAmount(_claim, _claim.endTimestamp);'
VTVLVesting.sol, 208, b'        return _baseVestedAmount(_claim, _claim.endTimestamp);'
VTVLVesting.sol, 208, b'        return _baseVestedAmount(_claim, _claim.endTimestamp);'
VTVLVesting.sol, 208, b'        return _baseVestedAmount(_claim, _claim.endTimestamp);'
VTVLVesting.sol, 208, b'        return _baseVestedAmount(_claim, _claim.endTimestamp);'
VTVLVesting.sol, 217, b'        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;'
VTVLVesting.sol, 217, b'        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;'
VTVLVesting.sol, 217, b'        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;'
VTVLVesting.sol, 217, b'        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;'
VTVLVesting.sol, 217, b'        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;'
VTVLVesting.sol, 217, b'        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;'
VTVLVesting.sol, 217, b'        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;'
VTVLVesting.sol, 217, b'        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;'
VTVLVesting.sol, 255, b'        require(_recipient != address(0), "INVALID_ADDRESS");'
VTVLVesting.sol, 256, b'        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both'
VTVLVesting.sol, 256, b'        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both'
VTVLVesting.sol, 256, b'        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both'
VTVLVesting.sol, 256, b'        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both'
VTVLVesting.sol, 257, b'        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");'
VTVLVesting.sol, 257, b'        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");'
VTVLVesting.sol, 262, b'        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp'
VTVLVesting.sol, 262, b'        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp'
VTVLVesting.sol, 263, b'        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");'
VTVLVesting.sol, 263, b'        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");'
VTVLVesting.sol, 264, b'        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");'
VTVLVesting.sol, 264, b'        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");'
VTVLVesting.sol, 264, b'        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");'
VTVLVesting.sol, 264, b'        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");'
VTVLVesting.sol, 280, b'        Claim memory _claim = Claim({'
VTVLVesting.sol, 281, b'            startTimestamp: _startTimestamp,'
VTVLVesting.sol, 282, b'            endTimestamp: _endTimestamp,'
VTVLVesting.sol, 283, b'            cliffReleaseTimestamp: _cliffReleaseTimestamp,'
VTVLVesting.sol, 284, b'            releaseIntervalSecs: _releaseIntervalSecs,'
VTVLVesting.sol, 285, b'            cliffAmount: _cliffAmount,'
VTVLVesting.sol, 286, b'            linearVestAmount: _linearVestAmount,'
VTVLVesting.sol, 287, b'            amountWithdrawn: 0,'
VTVLVesting.sol, 292, b'        uint112 allocatedAmount = _cliffAmount + _linearVestAmount;'
VTVLVesting.sol, 292, b'        uint112 allocatedAmount = _cliffAmount + _linearVestAmount;'
VTVLVesting.sol, 292, b'        uint112 allocatedAmount = _cliffAmount + _linearVestAmount;'
VTVLVesting.sol, 292, b'        uint112 allocatedAmount = _cliffAmount + _linearVestAmount;'
VTVLVesting.sol, 295, b'        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");'
VTVLVesting.sol, 295, b'        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");'
VTVLVesting.sol, 295, b'        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");'
VTVLVesting.sol, 301, b'        numTokensReservedForVesting += allocatedAmount; // track the allocated amount'
VTVLVesting.sol, 302, b'        vestingRecipients.push(_recipient); // add the vesting recipient to the list'
VTVLVesting.sol, 326, b'        _createClaimUnchecked(_recipient, _startTimestamp, _endTimestamp, _cliffReleaseTimestamp, _releaseIntervalSecs, _linearVestAmount, _cliffAmount);'
VTVLVesting.sol, 326, b'        _createClaimUnchecked(_recipient, _startTimestamp, _endTimestamp, _cliffReleaseTimestamp, _releaseIntervalSecs, _linearVestAmount, _cliffAmount);'
VTVLVesting.sol, 326, b'        _createClaimUnchecked(_recipient, _startTimestamp, _endTimestamp, _cliffReleaseTimestamp, _releaseIntervalSecs, _linearVestAmount, _cliffAmount);'
VTVLVesting.sol, 326, b'        _createClaimUnchecked(_recipient, _startTimestamp, _endTimestamp, _cliffReleaseTimestamp, _releaseIntervalSecs, _linearVestAmount, _cliffAmount);'
VTVLVesting.sol, 326, b'        _createClaimUnchecked(_recipient, _startTimestamp, _endTimestamp, _cliffReleaseTimestamp, _releaseIntervalSecs, _linearVestAmount, _cliffAmount);'
VTVLVesting.sol, 326, b'        _createClaimUnchecked(_recipient, _startTimestamp, _endTimestamp, _cliffReleaseTimestamp, _releaseIntervalSecs, _linearVestAmount, _cliffAmount);'
VTVLVesting.sol, 326, b'        _createClaimUnchecked(_recipient, _startTimestamp, _endTimestamp, _cliffReleaseTimestamp, _releaseIntervalSecs, _linearVestAmount, _cliffAmount);'
VTVLVesting.sol, 344, b'        require(_startTimestamps.length == length &&'
VTVLVesting.sol, 345, b'                _endTimestamps.length == length &&'
VTVLVesting.sol, 346, b'                _cliffReleaseTimestamps.length == length &&'
VTVLVesting.sol, 347, b'                _releaseIntervalsSecs.length == length &&'
VTVLVesting.sol, 348, b'                _linearVestAmounts.length == length &&'
VTVLVesting.sol, 349, b'                _cliffAmounts.length == length, '
VTVLVesting.sol, 354, b'            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);'
VTVLVesting.sol, 354, b'            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);'
VTVLVesting.sol, 354, b'            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);'
VTVLVesting.sol, 354, b'            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);'
VTVLVesting.sol, 354, b'            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);'
VTVLVesting.sol, 354, b'            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);'
VTVLVesting.sol, 354, b'            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);'
VTVLVesting.sol, 353, b'        for (uint256 i = 0; i < length; i++) {'
VTVLVesting.sol, 371, b'        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));'
VTVLVesting.sol, 371, b'        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));'
VTVLVesting.sol, 371, b'        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));'
VTVLVesting.sol, 371, b'        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));'
VTVLVesting.sol, 371, b'        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));'
VTVLVesting.sol, 374, b'        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");'
VTVLVesting.sol, 374, b'        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");'
VTVLVesting.sol, 374, b'        require(allowance > usrClaim.amountWithdrawn, "NOTHING_TO_WITHDRAW");'
VTVLVesting.sol, 377, b'        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;'
VTVLVesting.sol, 377, b'        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;'
VTVLVesting.sol, 377, b'        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;'
VTVLVesting.sol, 377, b'        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;'
VTVLVesting.sol, 377, b'        uint112 amountRemaining = allowance - usrClaim.amountWithdrawn;'
VTVLVesting.sol, 381, b'        usrClaim.amountWithdrawn += amountRemaining;'
VTVLVesting.sol, 383, b'        numTokensReservedForVesting -= amountRemaining;'
VTVLVesting.sol, 388, b'        tokenAddress.safeTransfer(_msgSender(), amountRemaining);'
VTVLVesting.sol, 400, b'        uint256 amountRemaining = tokenAddress.balanceOf(address(this)) - numTokensReservedForVesting;'
VTVLVesting.sol, 402, b'        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");'
VTVLVesting.sol, 407, b'        tokenAddress.safeTransfer(_msgSender(), _amountRequested);'
VTVLVesting.sol, 422, b'        uint112 finalVestAmt = finalVestedAmount(_recipient);'
VTVLVesting.sol, 422, b'        uint112 finalVestAmt = finalVestedAmount(_recipient);'
VTVLVesting.sol, 422, b'        uint112 finalVestAmt = finalVestedAmount(_recipient);'
VTVLVesting.sol, 426, b'        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");'
VTVLVesting.sol, 426, b'        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");'
VTVLVesting.sol, 426, b'        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");'
VTVLVesting.sol, 429, b'        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;'
VTVLVesting.sol, 429, b'        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;'
VTVLVesting.sol, 429, b'        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;'
VTVLVesting.sol, 429, b'        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;'
VTVLVesting.sol, 429, b'        uint112 amountRemaining = finalVestAmt - _claim.amountWithdrawn;'
VTVLVesting.sol, 433, b'        numTokensReservedForVesting -= amountRemaining; // Reduces the allocation'
VTVLVesting.sol, 449, b'        require(bal > 0, "INSUFFICIENT_BALANCE");'