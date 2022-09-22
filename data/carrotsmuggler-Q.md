## QA

1. Contract VariableSupplyERC20Token.sol has a variable named maxSupply, which has a different meaning than mintable supply. maxSupply's meaning is closer to mintablesupply + initialsupply, or the ceiling for totalsupply. It is better to rename it to maxMintableSupply or something similar, to make it clear that it is only counting tokens yet to be minted.