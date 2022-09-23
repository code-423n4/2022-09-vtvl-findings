# Rounding in `_baseVestedAmount` function leads to zero amount available for withdraw after some time passes 

## Impact
It has been identified that is possible for a user to not be able to withdraw any funds, even he has an active claim and some time has passed through the linear vesting period. 

This happens due to the rounding in the `_baseVestedAmount` function.

The available withdrawn amount is `0` when `linearVestAmount * truncatedCurrentVestingDurationSecs` < `finalVestingDurationSecs` 

Suppose that an administrator creates claim for `userA` with the values: 
```
address _recipient : user A 
uint40 _startTimestamp : uint40(10)
uint40 _endTimestamp: uint40(545443020800)
uint40 _cliffReleaseTimestamp: 0
uint40 _releaseIntervalSecs: 10
uint112 _linearVestAmount: uint112(3965)
uint112 _cliffAmount: 0
```

After `9000` seconds the amount that is available to be withdrawn will be 0.
This is inconvenient for the users as they try to withdraw the funds and the transaction reverts. 

## Proof of Concept
Bellow is a foundry test noting the issue
```solidity
pragma solidity ^0.8.13;

import "forge-std/Test.sol";
import "../src/test/TestERC20Token.sol";
import "../src/VTVLVesting.sol";


contract CounterTest is Test {
    address owner = address(1);
    address user1 = address(2);
    address user2 = address(3);
    TestERC20Token testERC20Token;
    VTVLVesting vtvlVesting;

    function setUp() public {
        //start a prank as the owner
        vm.startPrank(owner);
        //deploy a test token with 4000*10**18 initial supply
        testERC20Token = new TestERC20Token("testName", "testSymbol", 4000);

        //deploy the vtvlVesting contract
        vtvlVesting = new VTVLVesting(testERC20Token);

        //transfer 200 * 10 ** 18 tokens to the vtvlVesting smart contract
        testERC20Token.transfer(address(vtvlVesting), 900 * 10 ** 18);
  
        vm.stopPrank();       
    }
    
    function testVerifyZeroWithdraw() public {
        vm.prank(owner);

        vtvlVesting.createClaim(user1, uint40(10), uint40(545443020800), 0, 10, uint112(3965), 0);

        skip(9000);
        console.log(vtvlVesting.claimableAmount(user1));

        vm.startPrank(user1);
        vtvlVesting.withdraw();

        vm.stopPrank();
    }
}
```
