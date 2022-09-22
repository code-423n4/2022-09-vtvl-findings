-> X = X + Y IS CHEAPER THAN X += Y (same for X = X - Y IS CHEAPER THAN X -= Y)

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#:~:text=mintableSupply%20%2D%3D%20amount%3B
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#:~:text=vestAmt%20%2B%3D%20_claim.cliffAmount%3B
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#:~:text=vestAmt%20%2B%3D%20linearVestAmount%3B
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#:~:text=numTokensReservedForVesting%20%2B%3D%20allocatedAmount%3B
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#:~:text=usrClaim.amountWithdrawn%20%2B%3D%20amountRemaining%3B
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#:~:text=debt%22%20gets%20reduced-,numTokensReservedForVesting%20%2D%3D%20amountRemaining%3B,-//%20After%20the%20%22books


->SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#:~:text=at%20the%20_startTimestamp-,require(,)%2C%20%22INVALID_CLIFF%22)%3B,-Claim%20memory%20_claim
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#:~:text=require(_startTimestamps.,)%3B


-> ++i costs less gas compared to i++ or i += 1 (Also --i costs less gas compared to i--- or i -= 1)

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#:~:text=i%20%3C%20length%3B-,i%2B%2B),-%7B


->USING BOOLS FOR STORAGE INCURS OVERHEAD

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#:~:text=mapping(address%20%3D%3E%20bool)


