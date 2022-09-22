Issure 1.
No requirement of using greater than zero in requrie and if statement on L27 and L31 as it is already defined as 'uint' (uint256 initialSupply_, uint256 maxSupply_)....

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31


Issue 2.
'mintsupply' in mint function will cost a unrequired gas wastage as it is passing again to storage data....

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L35

function mint(address account, uint256 amount) public onlyAdmin {
        require(account != address(0), "INVALID_ADDRESS");
        // If we're using maxSupply, we need to make sure we respect it
        // mintableSupply = 0 means mint at will
        if(mintableSupply > 0) {
            require(amount <= mintableSupply, "INVALID_AMOUNT");
            // We need to reduce the amount only if we're using the limit, if not just leave it be
            mintableSupply -= amount;
        }
        _mint(account, amount);
    }

=> use another uint declaration inside a function as '_mintsupply' then pass it to storage data..as it will save a gas ...