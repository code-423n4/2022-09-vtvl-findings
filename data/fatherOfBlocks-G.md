**FullPremintERC20Token**
- L11 - When we make a comparison between two numbers it is much less expensive to do uint256() != 0 than uint256() > 0.


**VariableSupplyERC20Token**
- L27/31/40 - When performing a comparison between two numbers it is much less expensive to do uint256() != 0 than uint256() > 0.

**VTVLVesting**
- L27/148/353 - It is not necessary to set a variable with its default value, this generates an extra expense since a double setting is made.

- L107/256/257/263/272/273/449 - When performing a comparison between two numbers it is much less expensive to do uint256() != 0 than uint256() > 0.

- L111 - When we want to use a boolean to execute something or not, it is not necessary to do: validation == true or validation == false, this generates an extra gas cost. Validation or validation can be done directly

- L167/353/374/377/426/429 - When an operation has already been validated beforehand and we know that it does not generate an overflow or underflow, we can wrap the operation with unchecked so that it does not validate the overflow twice.

