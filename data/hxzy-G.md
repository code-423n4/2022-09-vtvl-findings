The Ownable library contract in the AccessProtected.sol file is not actually used in the contract, which may cause additional gas consumption during deployment. It is recommended to delete the import of the Ownable library contract.

The specific locations are as follows: https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L5
