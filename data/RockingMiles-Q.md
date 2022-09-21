## Duplicates in array

        You allow in some arrays to have duplicates. Sometimes you assumes there are no duplicates in the array.

### Code instance:

VTVLVesting._createClaimUnchecked pushed (_recipient) You may push _recipient twice but the size of the dictionary will not change after the first assignment. You will get a mismatch in between those two data structures. I put as low since the function name suggests that it's handled.


## Not verified input


external / public functions parameters should be validated to make sure the address is not 0.
Otherwise if not given the right input it can mistakenly lead to loss of user funds.    

### Code instances:

        VariableSupplyERC20Token.sol.mint account
        AccessProtected.sol.setAdmin admin
        VTVLVesting.sol.revokeClaim _recipient
        VTVLVesting.sol.createClaim _recipient



## Two Steps Verification before Transferring Ownership

The following contracts have a function that allows them an admin to change it to a different address. If the admin accidentally uses an invalid address for which they do not have the private key, then the system gets locked.
It is important to have two steps admin change where the first is announcing a pending new admin and the new address should then claim its ownership. 
A similar issue was reported in a previous contest and was assigned a severity of medium: [code-423n4/2021-06-realitycards-findings#105](https://github.com/code-423n4/2021-06-realitycards-findings/issues/105) 

### Code instance:

        AccessProtected.sol



## Open TODOs

Open TODOs can hint at programming or architectural errors that still need to be fixed. 
These files has open TODOs:

### Code instance:

Open TODO in VTVLVesting.sol line 265 :         // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?




## Check transfer receiver is not 0 to avoid burned money


Transferring tokens to the zero address is usually prohibited to accidentally avoid "burning" tokens by sending them to an unrecoverable zero address.


### Code instances:

        https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L405
        https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L450
        https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L380



## Add a timelock

To give more trust to users: functions that set key/critical variables should be put behind a timelock.

### Code instance:

        https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L39



## Div by 0


Division by 0 can lead to accidentally revert,
(An example of a similar issue - https://github.com/code-423n4/2021-10-defiprotocol-findings/issues/84)

### Code instance:

        https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L169 _claim might be 0



## Tokens with fee on transfer are not supported


There are ERC20 tokens that charge fee for every transfer() / transferFrom().

Vault.sol#addValue() assumes that the received amount is the same as the transfer amount, 
and uses it to calculate attributions, balance amounts, etc. 
But, the actual transferred amount can be lower for those tokens.
Therefore it's recommended to use the balance change before and after the transfer instead of the amount.
This way you also support the tokens with transfer fee - that are popular.


### Code instance:

        https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L380


