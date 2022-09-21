# Gas Optimization


## ++i costs less gas than i++ and i+=1 (--i/i--/i-=1 too)

        File: contracts/VTVLVesting.sol

        for (uint256 i = 0; i < length; i++) {

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353


## Initializing a variable to its default value costs unnecessary gas.

        File: contracts/VTVLVesting.sol

    uint112 public numTokensReservedForVesting = 0;

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27

        File: contracts/VTVLVesting.sol

        uint112 vestAmt = 0;

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148

        File: contracts/VTVLVesting.sol

        for (uint256 i = 0; i < length; i++) {

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353


## Variable increment(e.g.++i/i++) for looping should be unchecked{++i} when they are not possible to overflow, to remove overflow checking to save gas.

        File: contracts/VTVLVesting.sol

        for (uint256 i = 0; i < length; i++) {

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353


## Declare errors for revert, instead of using string, to reduce gas.

        File: contracts/VTVLVesting.sol

        require(address(_tokenAddress) != address(0), "INVALID_ADDRESS");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L82

        File: contracts/VTVLVesting.sol

        require(_claim.startTimestamp > 0, "NO_ACTIVE_CLAIM");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107

        File: contracts/VTVLVesting.sol

        require(_claim.isActive == true, "NO_ACTIVE_CLAIM");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111

        File: contracts/VTVLVesting.sol

        require(_claim.startTimestamp == 0, "CLAIM_ALREADY_EXISTS");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L129

        File: contracts/VTVLVesting.sol

        require(_recipient != address(0), "INVALID_ADDRESS");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255

        File: contracts/VTVLVesting.sol

        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256

        File: contracts/VTVLVesting.sol

        require(_startTimestamp > 0, "INVALID_START_TIMESTAMP");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L257

        File: contracts/VTVLVesting.sol

        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L262

        File: contracts/VTVLVesting.sol

        require(_releaseIntervalSecs > 0, "INVALID_RELEASE_INTERVAL");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263

        File: contracts/VTVLVesting.sol

        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L264

        File: contracts/VTVLVesting.sol

        require( 
            (
                _cliffReleaseTimestamp > 0 && 
                _cliffAmount > 0 && 
                _cliffReleaseTimestamp <= _startTimestamp
            ) || (
                _cliffReleaseTimestamp == 0 && 
                _cliffAmount == 0
        ), "INVALID_CLIFF");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L278

        File: contracts/VTVLVesting.sol

        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L295

        File: contracts/VTVLVesting.sol

        require(_startTimestamps.length == length &&
                _endTimestamps.length == length &&
                _cliffReleaseTimestamps.length == length &&
                _releaseIntervalsSecs.length == length &&
                _linearVestAmounts.length == length &&
                _cliffAmounts.length == length, 
                "ARRAY_LENGTH_MISMATCH"
        );

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L350

        File: contracts/VTVLVesting.sol

        require(amountRemaining >= _amountRequested, "INSUFFICIENT_BALANCE");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L402

        File: contracts/VTVLVesting.sol

        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L426

        File: contracts/VTVLVesting.sol

        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L447

        File: contracts/VTVLVesting.sol

        require(bal > 0, "INSUFFICIENT_BALANCE");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449

        File: contracts/AccessProtected.sol

        require(_admins[_msgSender()], "ADMIN_ACCESS_REQUIRED");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L25

        File: contracts/AccessProtected.sol

        require(admin != address(0), "INVALID_ADDRESS");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L40

        File: contracts/token/FullPremintERC20Token.sol

        require(supply_ > 0, "NO_ZERO_MINT");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11

        File: contracts/token/VariableSupplyERC20Token.sol

        require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27

        File: contracts/token/VariableSupplyERC20Token.sol

        require(account != address(0), "INVALID_ADDRESS");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L37

        File: contracts/token/VariableSupplyERC20Token.sol

            require(amount <= mintableSupply, "INVALID_AMOUNT");

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41


## Consider inline the logic instead of using a modifier, which is only used once, to reduce contract size and deployment gas.

        File: contracts/VTVLVesting.sol

    modifier hasNoClaim(address _recipient) {

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L123

