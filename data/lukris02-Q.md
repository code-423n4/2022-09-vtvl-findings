# QA Report for VTVL contest

## Overview
During the audit, 1 low and 8 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | [Large number of elements may cause out-of-gas error](#l-1-large-number-of-elements-may-cause-out-of-gas-error) | Low | 1
NC-1 | [Order of Functions](#nc-1-order-of-functions) | Non-Critical | 5
NC-2 | [Order of Layout](#nc-2-order-of-layout) | Non-Critical | 3
NC-3 | [Floating pragma](#nc-3-floating-pragma) | Non-Critical | 1
NC-4 | [Typo](#nc-4-typo) | Non-Critical | 1
NC-5 | [Public functions can be external](#nc-5-public-functions-can-be-external) | Non-Critical | 2
NC-6 | [Missing NatSpec](#nc-6-missing-natspec) | Non-Critical | 2
NC-7 | [Open TODO](#nc-7-open-todo) | Non-Critical | 1
NC-8 | [Maximum line length exceeded](#nc-8-maximum-line-length-exceeded) | Non-Critical | 5

## Low Risk Findings (1)
### L-1. Large number of elements may cause out-of-gas error
##### Description
[Loops](https://docs.soliditylang.org/en/develop/security-considerations.html#gas-limit-and-loops) that do not have a fixed number of iterations, for example, loops that depend on storage values, have to be used carefully: Due to the block gas limit, transactions can only consume a certain amount of gas. Either explicitly or just due to normal operation, the number of iterations in a loop can grow beyond the block gas limit, which can cause the complete contract to be stalled at a certain point. 

##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

##### Recommendation
Restrict the maximum number of elements.

## Non-Critical Risk Findings (8)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private

##### Instances
1) [internal function before public](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L147) 
3) [public functions before external](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L196) and [(2)](
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L206), [(3)](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398)
4) [private function before external](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L253) 

##### Recommendation
Reorder functions where possible.

#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

##### Instances
- [modifiers between functions](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L24) and [(2)](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L105), [(3)](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L123)

##### Recommendation
Place modifiers before constructor.

#
### NC-3. Floating pragma
##### Description
Contracts should be deployed with the same compiler version. It helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol

##### Recommendation
According to [SWC-103](https://swcregistry.io/docs/SWC-103), pragma version should be locked.

#
### NC-4. Typo
##### Description
Typo in the comment.

##### Instances
[```// Next, we need to calculated the duration truncated to nearest releaseIntervalSecs```](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L168)

##### Recommendation
Change to:  
"// Next, we need to **calculate** the duration truncated to nearest releaseIntervalSecs"

#
### NC-5. Public functions can be external
##### Description
If functions are not called by the contract where they are defined, they can be declared external.

##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L39
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L398

##### Recommendation
Make public functions external, where possible.

#
### NC-6. Missing NatSpec
##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L36
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L29

##### Recommendation
Add NatSpec for all functions.

#
### NC-7. Open TODO
##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L266

##### Recommendation
Resolve issues.

#
### NC-8. Maximum line length exceeded
##### Description
Some lines of code are too long.

##### Instances
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L21
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L169
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L295
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L326
- https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L354

##### Recommendation
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.  
Make the lines shorter.