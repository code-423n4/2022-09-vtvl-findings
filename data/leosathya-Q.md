### [L-01] Use of Floating Pragma

Use of Floating(^) Pragma in Contract file VariableSupplyERC20Token.sol

There is 1 instance of this issue:

> ** File :  token/VariableSupplyERC20Token.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2

#### Recommended Mitigation

Contracts should be deployed using the same compiler version with which they have been tested. Locking the pragma (for e.g. by not using ^ in pragma solidity 0.8.14) ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs. 




### [L-02] setAdmin() Should be a 2 step process

There is 1 instance of this issue:

The critical procedures like Admin change should always be a two step process.

> **File :  ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39-L44

#### Proof Of Concept

It would be much better if we use a 2 step process for critical address change In a smartContract, like here we can create 2 functions setAdmin() and updateAdmin().
Here setAdmin() function keeps new_address in storage rather than modifying current Admin address. Then updateAdmin() functions checks whether msg.sender == new_address or not which means new Admin signs transaction and verifies himself as the new Admin, after then Admin storage will updated with new_address.


#### Recommended Mitigation

Lack of two-step procedure for critical operations leaves them error-prone. 
Consider adding two step procedure on the critical functions.




### [L-03] Division before Multiplication

Performing multiplication before division is generally better to avoid loss of precision because Solidity integer division might truncate.

> **File :  VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L169


#### Recommended Mitigation
prefer multiplication before division.
i.e Instead (a/b)*c use (a*c)/b this gives more accurate result 


 ### [N-01] Use msg.sender instead of _msgSender()

_msgSender() is a replacement for msg.sender. For regular transactions it returns msg.sender and for meta transactions it can be used to return the end user (rather than the relayer).

There are 12 instances of this issue:
>** File : VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L364
>** File : VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L367
>** File : VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L371
>** File : VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L388
>** File : VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L391
>** File : VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L407
>** File : VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L410
>** File : VTVLVesting.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L450
>** File : AccessProtected.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L17
>** File : AccessProtected.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L18
>** File : AccessProtected.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L25
>** File : token/FullPremintERC20Token.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L12
>** File : token/VariableSupplyERC20Token.sol ** => https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L32


#### Recommended Mitigation
It is recommended to use msg.sender instead of _msgSender(), If the corresponding contract is not dealing with meta transaction, or don't have any meta transaction implementation.