### Initializing variable to default value
The variable numTokensReservedForVesting is being initialized to zero and the uint default value is already zero.

### Using custom errors is more gas efficient
Starting from Solidity 0.8.4 using custom errors instead of strings is more gas efficient.

### Require statement not needed due to underflow
VariableSupplyERC20Token.mint
```
            require(amount <= mintableSupply, "INVALID_AMOUNT");
            mintableSupply -= amount;
```
The require statement is not needed here cause the operation will revert in case of underflow