# Gas Optimizations
* Cache the return value of `_msgSender()` instead of calling it multiple times. This issue can be seen in multiple functions:
  * `AccessProtected.constructor()`
  * `VTVLVesting.withdraw()`
  * `VTVLVesting.withdrawAdmin()`
* Re-arrange the order of the Claim struct's members to reduce its storage size. Currently, the struct takes 3 storage slots:
    ```sol
    struct Claim {
        // First slot - 160 bits
        uint40 startTimestamp
        uint40 endTimestamp;
        uint40 cliffReleaseTimestamp;
        uint40 releaseIntervalSecs;
            
        // Second slot - 224 bits
        uint112 linearVestAmount
        uint112 cliffAmount

        // Third slot - 120 bits
        uint112 amountWithdrawn;
        bool isActive;
        }
    ```
    However, if the members will be in this order, and the releaseIntervalSecs (which will fit in less than 40 bits) will be saved as a uint32, it will take only 2 storage slots (with even 32 total free bits):
    ```sol
    struct Claim {
        // First slot - 240 bits
        uint112 linearVestAmount
        uint40 startTimestamp
        uint40 endTimestamp;
        uint40 cliffReleaseTimestamp;
        bool isActive;
        
        // Second slot - 256 bits
        uint112 cliffAmount
        uint112 amountWithdrawn;
        uint32 releaseIntervalSecs;
        }
    ```
    > Note: additional 16 bits can be saved in the first slot, so if the size of releaseIntervalSecs is not enough we can add more information for it in the first slot, but it is supposed to be enough (maximum interval of `2 ** 32 - 1 `).
* Don't initialize variables to their default value - it is done automatically for you, and it actually costs more if you do it yourself. This can be seen in:
  * VTVLVesting [line 27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27)
  * VTVLVesting [line 148](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148)
  * VTVLVesting [line 353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)
* The `_baseVestedAmount` checks first if `_referenceTs >= _claim.cliffReleaseTimestamp`, and then if `_referenceTs > _claim.startTimestamp`. But we know that `_claim.cliffReleaseTimestamp <= _claim.startTimestamp`, so we don't need to check the first condition if the second is met. The code should look like this:
    ```sol
        // Calculate the linearly vested amount - this is relevant only if we're past the schedule start
        // at _referenceTs == _claim.startTimestamp, the period proportion will be 0 so we don't need to start the calc
        if(_referenceTs > _claim.startTimestamp) {
            vestAmt += _claim.cliffAmount;

            uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start
            // Next, we need to calculated the duration truncated to nearest releaseIntervalSecs

            uint40 truncatedCurrentVestingDurationSecs = (currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs;
            uint40 finalVestingDurationSecs = _claim.endTimestamp - _claim.startTimestamp; // length of the interval

            // Calculate the linear vested amount - fraction_of_interval_completed * linearVestedAmount
            // Since fraction_of_interval_completed is truncatedCurrentVestingDurationSecs / finalVestingDurationSecs, the formula becomes
            // truncatedCurrentVestingDurationSecs / finalVestingDurationSecs * linearVestAmount, so we can rewrite as below to avoid 
            // rounding errors
            uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;

            // Having calculated the linearVestAmount, simply add it to the vested amount
            vestAmt += linearVestAmount;
        } else if (_referenceTs >= _claim.cliffReleaseTimestamp) {
            vestAmt += _claim.cliffAmount;
        }
    ```
* Add the unchecked modifier on calculations that we know that won't underflow or overflow. This can be seen in:
  * [VTVLVesting line 167](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L167)
  * [VTVLVesting line 170](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L170)
  * [VTVLVesting line 264](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L264)
  * [VTVLVesting line 353 (loop index incrementation)](https://github.com/code-423n4/2022-09-vtvl/blob/m353/contracts/VTVLVesting.sol#L429)
  * [VTVLVesting line 377](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L377)
  * [VTVLVesting line 429](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L429)
  * [VariableSupplyERC20Token line 43](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L43)
* Use `currentVestingDurationSecs - currentVestingDurationSecs % _claim.releaseIntervalSecs` instead of `(currentVestingDurationSecs / _claim.releaseIntervalSecs) * _claim.releaseIntervalSecs`
* Save the vesting duration as a member of the Claim struct instead of calculating it every time (_claim.endTimestamp - _claim.startTimestamp) in the `_baseVestedAmount` function
* Use `calldata` instead of `memory` when passing struct or array arguments to functions. This can be seen in the `createClaimsBatch` function.
* Use `memory` instead of `storage` when fetching the Claim struct of a user inside a function to avoid multiple SLOADs to get the different values of the struct. This can be seen at:
  * [line 106](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L106)
  * [line 420](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L420)
* Cache the calculation of `_linearVestAmount + _cliffAmount` in [line 256](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256) in the `_createClaimUnchecked` function. This calculation is done again in the end of this function ([line 292](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L292)), and caching it will save gas.
* Cache the value of `numTokensReservedForVesting + allocatedAmount` in [line 295](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L295), and store it later in `numTokensReservedForVesting` instead of adding `allocatedAmount` to it again. This will both save gas for the double calculation and will save an SLOAD.
* Use prefix incrementation instead of postfix incrementation for loops, i.e. `++i` instead of `i++`. This will save gas and has the same effect in this case.
* Cache `usrClaim.amountWithdrawn` in the `withdraw` and `revokeClaim` functions to save SLOADs. This value is accessed multiple times, and caching it will reduce the number of SLOADs.
* Implement the constructor of the `VariableSupplyERC20Token` contract differently to save gas spent on:
1. Double processing of the same expression
2. Multiple checks and storage operation due to calling the mint function
3. Redundant check that the message sender is not the zero address
The new code will look like this:
```sol
constructor(string memory name_, string memory symbol_, uint256 initialSupply_, uint256 maxSupply_) ERC20(name_, symbol_) {
        // max supply == 0 means mint at will. 
        // initialSupply_ == 0 means nothing preminted
        // Therefore, we have valid scenarios if either of them is 0
        // However, if both are 0 we might have a valid scenario as well - user just wants to create a token but doesn't want to mint anything
        // Should we allow this?
        bool initialSupplyPositive = initialSupply_ > 0;
        require(initialSupplyPositive || maxSupply_ > 0, "INVALID_AMOUNT");
        
        if(initialSupplyPositive) {
            require(initialSupply_ <= maxSupply_, "INVALID_AMOUNT");
            unchecked {
                maxSupply_ -= initialSupply_;
            }
            _mint(_msgSender(), initialSupply_);
        }

        mintableSupply = maxSupply_;
    }
```
* Use `!= 0` instead of `> 0` when comparing `uint`s to 0. This can be seen in:
    * VTVLVesting [line 110](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L110)
    * VTVLVesting [line 268](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L268)
    * VTVLVesting [line 269](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L269)
    * VTVLVesting [line 275](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L275)
    * VTVLVesting [line 286](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L286)
    * VTVLVesting [line 287](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L287)
    * VTVLVesting [line 473](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L473)
    * FullPremintERC20Token [line 11](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11)
    * VariableSupplyERC20Token [line 27](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27)
    * VariableSupplyERC20Token [line 31](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31)
    * VariableSupplyERC20Token [line 40](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40)
