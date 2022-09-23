1)Use custom errors rather than revert()/require() strings to save gas


File: 2022-09-vtvl\contracts\token\FullPremintERC20Token.sol
  11,9:         require(supply_ > 0, "NO_ZERO_MINT");
  
File: 2022-09-vtvl\contracts\token\FullPremintERC20Token.sol:
  11:         require(supply_ > 0, "NO_ZERO_MINT");


File: 2022-09-vtvl\contracts\token\VariableSupplyERC20Token.sol:
  
  27:         require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");


 
  37:         require(account != address(0), "INVALID_ADDRESS");

  41:             require(amount <= mintableSupply, "INVALID_AMOUNT");

2) USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT IN A REQUIRE() STATEMENT

This change saves 6 gas per instance

File: 2022-09-vtvl\contracts\token\FullPremintERC20Token.sol
  11,25:         require(supply_ > 0, "NO_ZERO_MINT");

File: 2022-09-vtvl\contracts\token\VariableSupplyERC20Token.sol
  27,32:         require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
  27,50:         require(initialSupply_ > 0 || maxSupply_ > 0, "INVALID_AMOUNT");
 