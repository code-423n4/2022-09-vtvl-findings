
# QA
# Low
## block.timestamp used as time proxy 
### Summary
Risk of using block.timestamp for time should be considered. 
### Details
block.timestamp is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L426
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L295
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L374
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L402
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L154
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L160
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L166
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L187

### Mitigation
- Consider using an oracle for precision
- Consider the risk of using block.timestamp as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 

## Loop can lead to out of gas
### Impact
Expensive operations with unbounded loops can lead into out of gas errors
### Details
Loop call includes for example += of state variable which is known of having high cost of gas
`numTokensReservedForVesting += allocatedAmount;`
### Github permalink
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L354

# Informational
## Comparison with a a boolean
### Summary
There are a number of instances where a boolean variable/function is checked.
### Details
- This check can be further simplified from `variable == true` to `variable`.

### Github Permalink
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L111
### Mitigation
Simplify boolean comparisons in order to improve readability and save gas


## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity ^0.8.14. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L2

### Mitigation
Lock pragmas to a specific Solidity version. 
Consider converting ^0.8.14 into 0.8.14

## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
As Solidity guidelines says about declaration order:
1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions
Also, state variables order affects to gas in the same way as ordering structs for saving storage slots
### Details
Modifiers are expected to be before functions, however, two of them are placed after functions.
### github permalink
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L105-L140


### Mitigation
Follow solidity style guidelines https://docs.soliditylang.org/en/v0.8.15/style-guide.html

## Missing Natspec 
### Summary 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 
### Details 
Contracts has partial or full lack of comments
### Github Permalinks 
#### Natspec @param
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L29-L31
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L418
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L333
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L105-L123
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L81-L84
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L36-L46
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/FullPremintERC20Token.sol#L10-L13

#### Natspec @return value
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L29
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L91
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L147
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L196
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L206
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L215
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L223
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L230
 
 ### mitigation
 - Add @param descriptors
 - Add @return descriptors
 - Add Natspec comments. 
 - Add comments for what the contract does

## Commented out code
### Summary
There is some code that is commented out. It could point to items that are not done or need redesigning, be a mistake, or just be testing overhead.

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L261
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L136-L138
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L133
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L114-L115
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/FullPremintERC20Token.sol#L9
### Mitigation
Review and remove or resolve/document the commented out lines if needed.


## Open TODOs   
### Summary
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
### Details
The code includes a TODO already done that affects readability and focus on the readers/auditors of the contracts
### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L266
        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?

### Mitigation
Remove already done TODO

## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks 
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/FullPremintERC20Token.sol#L7

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/FullPremintERC20Token.sol#L10

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L14

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L18

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L19

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L21

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L25

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L30

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/token/VariableSupplyERC20Token.sol#L49

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L9

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L23

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L24

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L34

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L36

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L37

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L44

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L69

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L88

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L135

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L143

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L147

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L164

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L165

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L167

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L168

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L169

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L170

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L172

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L173

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L174

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L176

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L184

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L185

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L186

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L202

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L212

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L235

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L236

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L240

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L241

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L242

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L243

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L256

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L258

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L260

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L261

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L262

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L264

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L266

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L269

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L290

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L294

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L295

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L308

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L312

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L313

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L314

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L315

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L326

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L330

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L354

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L357

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L369

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L382

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L400

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L405

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L428

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L432

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L441

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L442

https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L447
### Mitigation
Reduce line length to less than 99 at least to improve maintainability and readability of the code 

## State variables that do not change should be constant and written in UPPERCASE
### Summary
`constant` keyword helps with readability of the code and to make sure that they do not change. 

### Details
Code contains state variables that do not change and so they can be declared `constant`

### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L27

### Mitigation
Add constant and change `numTokensReservedForVesting` to `NUM_TOKENS_RESERVED_FOR_VESTING`

## Unused code
### Summary
Code that is not used should be removed
### Details
- @openzeppelin/contracts/access/Ownable.sol is being imported but not used
### Github Permalinks
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/AccessProtected.sol#L5
https://github.com/code-423n4/2022-09-vtvl/blob/26dda235d38d0f870c1741c9f7eef03229172bbe/contracts/VTVLVesting.sol#L6
### Mitigation
Remove the code that is not used.
