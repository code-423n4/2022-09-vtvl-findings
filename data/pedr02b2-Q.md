### Remove dev comments

Remove these types devs comments after changes have been made or development decisions have been made to the code base as a standard practice (ideally these should have been removed before code audit) , or at a minimum remove said comments before deployment to mainnet to stop unsavoury characters from seeing these and potentially using these types of comments to find potential flaws in the project

        line 256 // Actually only one of linearvested/cliff amount must be 0, not necessarily both

        line 258 // Do we need to check whether _startTimestamp is greater than the current block.timestamp? 
        line 259 // Or do we allow schedules that started in the past? 
        line 260 // -> Conclusion: we want to allow this, for founders that might have forgotten to add some users, or to avoid issues with transactions 
                               not going through because of discoordination between block.timestamp and sender's local time
        line 261 // require(_endTimestamp > 0, "_endTimestamp must be valid"); // not necessary because of the next condition (transitively)
                     require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp

         line 266 // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?

        line 268 // No point in allowing cliff TS without the cliff amount or vice versa.
        line 269 // Both or neither of _cliffReleaseTimestamp and _cliffAmount must be set. If cliff is set, _cliffReleaseTimestamp must be before or at the 
                     _startTimestamp


### Only check _linearVestedAmount

I was not sure what level to report this as I am still learning so I have left it to the judges to judge validity and threat level to the project 

Only check _linerVestAmount is > 0, as your only ever going to return if said amount it greater than 0, this does not return a check for _cliffAmount  (this should return a value of INVALID_CLIFF_AMOUNT as well to return a check on both values) even though your asking for a check to be carried out on _linearVestAmount + _cliffAmount, return a value for both "INVALID_VESTED_AMOUNT + INVALID_CLIFF_AMOUNT" if they are both invalid, 

however I also noted that there is the possibilty for _cliffAmount to not be zero if there is vesting??? (_cliffReleaseTimestamp - The timestamp when the cliff is released (must be <= _startTimestamp, or 0 if no vesting)) also noted by the dev cliffAmount - The amount released at _cliffReleaseTimestamp. Can be 0 if _cliffReleaseTimestamp is also 0, what if _cliffReleaseTimestamp is not 0 and there is vesting .. would be safer to do a check on just _linearVestAmount as noted by the dev so that claim creation cannot break accidentelty or otherwise

Also checking just the one value will make the contract slightly cheaper to deploy decreasing overall cost on the life cycle of the project.

line256 VTVLvesting.sol

require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT");

linearVestAmount - The total amount to be linearly vested between _startTimestamp and _endTimestamp
cliffAmount - The amount released at _cliffReleaseTimestamp. Can be 0 if _cliffReleaseTimestamp is also 0.

