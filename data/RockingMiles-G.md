## Prefix increments are cheaper than postfix increments

Prefix increments are cheaper than postfix increments. 
Further more, using unchecked {++x} is even more gas efficient, and the gas saving accumulates every iteration and can make a real change
There is no risk of overflow caused by increamenting the iteration index in for loops (the `++i` in `for (uint256 i = 0; i < numIterations; ++i)`).
But increments perform overflow checks that are not necessary in this case.
These functions use not using prefix increments (`++x`) or not using the unchecked keyword: 

### Code instance:

        change to prefix increment and unchecked: VTVLVesting.sol, i, 353



## Unnecessary index init


In for loops you initialize the index to start from 0, but it already initialized to 0 in default and this assignment cost gas. 
It is more clear and gas efficient to declare without assigning 0 and will have the same meaning:

### Code instance:

        VTVLVesting.sol, 353



## Unnecessary default assignment


Unnecessary default assignments, you can just declare and it will save gas and have the same meaning.
    

### Code instance:

        VTVLVesting.sol (L#27) : uint112 public numTokensReservedForVesting = 0; 



## Use != 0 instead of > 0


Using != 0 is slightly cheaper than > 0. (see https://github.com/code-423n4/2021-12-maple-findings/issues/75 for similar issue)


### Code instances:

        VTVLVesting.sol, 295: change 'balance > 0' to 'balance != 0'
        VTVLVesting.sol, 273: change '_cliffAmount > 0' to '_cliffAmount != 0'
        VariableSupplyERC20Token.sol, 27: change 'initialSupply_ > 0' to 'initialSupply_ != 0'
        VTVLVesting.sol, 256: change '_cliffAmount > 0' to '_cliffAmount != 0'
