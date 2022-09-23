## [NAZ-L1] Be Aware of The Use of Elastic Supply Tokens & Other Weird ERC20s
**Severity**: Informational
**Context**: [`VTVLVesting.sol`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol)

**Description**:
Elastic supply tokens could dynamically adjust their price, supply, user's balance, etc. Such a mechanism makes a DeFi system complex, while many security accidents are caused by the elastic tokens. For example, a DEX using deflationary token must double check the token transfer amount when taking swap action because of the difference of actual transfer amount and parameter.

**Recommendation**:
In terms of confidentiality, integrity and availability, it is highly recommend that one should not use elastic supply tokens & [weird-ERC20 tokens](https://github.com/d-xo/weird-erc20).



## [NAZ-L2] Unsafe Type Casting
**Severity** Low
**Context**: [`VTVLVesting.sol#L217`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L217), [`VTVLVesting.sol#L371`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#l371), [`VTVLVesting.sol#L436`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#l436) 

**Description**:
Downcasting from uint256/int256 in Solidity does not revert on overflow. This can easily result in undesired exploitation or bugs.

**Recommendation**:
Consider using SafeCast library from [OpenZeppelin](https://docs.openzeppelin.com/contracts/4.x/api/utils#SafeCast).


## [NAZ-L3] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`AccessProtected.sol#L39`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-N1] Visibility Should Be Before Modifiers
**Severity**: Informational
**Context**: [`VTVLVesting.sol#L364`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#l364)

**Description**:
Visibility keyword should be before the list of modifiers as per best practice.

**Recommendation**:
Consider moving the visibility keyword before the list of modifiers.


## [NAZ-N2] Line Length
**Severity**: Informational
**Context**: [`VariableSupplyERC20Token.sol#L19`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L19), [`VariableSupplyERC20Token.sol#L21`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L21), [`VariableSupplyERC20Token.sol#L25`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L25), [`VariableSupplyERC20Token.sol#L49`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L49), [`AccessProtected.sol#L9`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L9), [`VTVLVesting.sol#L34`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L34), [`VTVLVesting.sol#L36`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L36), [`VTVLVesting.sol#L69`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L69), [`VTVLVesting.sol#L88`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L88), [`VTVLVesting.sol#L165`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#l165), [`VTVLVesting.sol#L169`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L169), [`VTVLVesting.sol#L173-L174`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L173-L174), [`VTVLVesting.sol#L176`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L176), [`VTVLVesting.sol#L184-L185`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L184-L185), [`VTVLVesting.sol#L202`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L202), [`VTVLVesting.sol#L212`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L212), [`VTVLVesting.sol#L235`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L235), [`VTVLVesting.sol#L240-L241`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#l240-L241), [`VTVLVesting.sol#L256`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256), [`VTVLVesting.sol#L260-L262`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L260-L262), [`VTVLVesting.sol#L266`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L266), [`VTVLVesting.sol#L269`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L269), [`VTVLVesting.sol#L294-L295`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L294-L295), [`VTVLVesting.sol#L312-L313`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L312-L313), [`VTVLVesting.sol#L326`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L326), [`VTVLVesting.sol#L330`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L330), [`VTVLVesting.sol#L354`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L354), [`VTVLVesting.sol#L357`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L357), [`VTVLVesting.sol#L369`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L369), [`VTVLVesting.sol#L432`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L432), [`VTVLVesting.sol#L447`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#l447)

**Description**:
Max line length must be no more than 120 but many lines are extended past this length.

**Recommendation**:
Consider cutting down the line length below 120.


## [NAZ-N3] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`VTVLVesting.sol#L50`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L50), [`VTVLVesting.sol#L53`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L53)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `_underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html). 


## [NAZ-N4] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`AccessProtected.sol#L24`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L24), [`VTVLVesting.sol#L32`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L32)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Functions should then further be ordered with view functions coming after the non-view labeled ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N5] Commented Out Code
**Severity**: Informational
**Context**: [`FullPremintERC20Token.sol#L9`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L9), [`VTVLVesting.sol#L114-L115`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L114-L115), [`VTVLVesting.sol#L133`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L133), [`VTVLVesting.sol#L136-L138`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L136-L138)

**Description**:
There is commented code that makes the code messy and unneeded. 

**Recommendation**:
Consider removing the commented out code.


## [NAZ-N6] TODOs Left In The Code
**Severity**: Informational
**Context**: [`VTVLVesting.sol#L266`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L266)

**Description**:
There should never be any TODOs in the code when deploying.

**Recommendation**:
Consider finishing the TODOs before deploying.


## [NAZ-N7] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.


## [NAZ-N8] Floating Pragma
**Severity**: Informational
**Context**: [`VariableSupplyERC20Token.sol`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol)

**Description**:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

**Recommendation**: 
Consider locking the pragma version.


## [NAZ-N9] Older Version Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts)

**Description**:
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. 

**Recommendation**:
Consider using the most recent version.