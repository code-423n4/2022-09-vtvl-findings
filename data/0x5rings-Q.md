Findings: SetAdmin Visibility Modifier  should be marked External:

File: contracts/AccessProtected.sol
	- Line 39

Explanation: 
Assuming set admin isn't being called within the contract `AccessProtected.sol`.
setAdmin visibility modifier should be marked external.

----

Findings:  You should multiply before divide. No zero checks for finalVestionDuractionSecs

Code: Contract/VTVLVesting.sol 
	- Line 176

Explanation: Given Solidty works in integer the following value would be truncated as a result leading to loss of precision.

Mitigation: In general, it's usually a good idea to re-arrange arithmetic to perform multiplication before division, unless the limit of a smaller type makes this dangerous.

----

Findings: Multiple require statement

Code: contracts/VTVLVesting.sol
	- Line 343

Explanation: Splitting require() statements that use && saves gas

----

Findings:  Costly loop, gas optimization initialize of -, ++i, run out of gas

Code: contract/VTVLVesting.sol
	- Line 353

Explanation: Costly operations inside a loop might waste gas, so optimizations are justfiied in _createClaimUnchecked.

----

Findings: No check for 0x00 address on mint

Code: VariableSupplyERC20Token.sol

Explanation: No check for 0x00 address on mint on mint would lead to token being essentially burned. However given this is within the contructor and called once this may not be viewed as a major vulnerability.

Mitigation: Add 0x00 require checks.

----

## `++i` cost less gas compared to `i++`. Consider using `++{variable}` instead of `{variable}++`
---
### Files Found:
There are 2 instances of this issue.
- File: contracts/VTVLVesting.sol - line 353
---
### Mitigation:
Consider using `++i` instead of `i++`
## Constants should be defined rather than using magic numbers

---

### Mitigation:
It is best practice to use constant variables rather than literal values to make the code easier to understand and maintain.
## `x += y` costs more gas than `x = x + y` for state variables
---
### Files Found:
There are 4 instances of this issue.
- File: contracts/VTVLVesting.sol - line 161
- File: contracts/VTVLVesting.sol - line 179
- File: contracts/VTVLVesting.sol - line 301
- File: contracts/VTVLVesting.sol - line 381
---
### Mitigation:
Use `x = x + y` instead of `x += y`

## It costs more gas to initialise non-constant/non-immutable variables to zero than to let the default of zero be applied
---
### Files Found:
There are 1 instances of this issue.
- File: contracts/VTVLVesting.sol - line 353
---
### Mitigation:
uint is initialised at 0. It cost more gas to initialise variable at 0
## Visibility modifier must be first in list of modifiers
---
### Files Found:
There are 1 instances of this issue.
- File: contracts/VTVLVesting.sol - line 364
---
## Avoid to make time-based decisions in your business logic
---
### Files Found:
There are 3 instances of this issue.
- File: contracts/VTVLVesting.sol - line 217
- File: contracts/VTVLVesting.sol - line 371
- File: contracts/VTVLVesting.sol - line 436
---
## Visibility modifier must be first in list of modifiers
---
### Files Found:
There are 1 instances of this issue.
- File: contracts/VTVLVesting.sol - line 364
--- 
## Avoid to make time-based decisions in your business logic
---
### Files Found:
There are 3 instances of this issue.
- File: contracts/VTVLVesting.sol - line 217
- File: contracts/VTVLVesting.sol - line 371
- File: contracts/VTVLVesting.sol - line 436
---
## Visibility modifier must be first in list of modifiers
---
### Files Found:
There are 1 instances of this issue.
- File: contracts/VTVLVesting.sol - line 364
---
## Avoid to make time-based decisions in your business logic
---
### Files Found:
There are 3 instances of this issue.
- File: contracts/VTVLVesting.sol - line 217
- File: contracts/VTVLVesting.sol - line 371
- File: contracts/VTVLVesting.sol - line 436
---
## Visibility modifier must be first in list of modifiers
---
### Files Found:
There are 1 instances of this issue.
- File: contracts/VTVLVesting.sol - line 364
---
## Avoid to make time-based decisions in your business logic
---
### Files Found:
There are 3 instances of this issue.
- File: contracts/VTVLVesting.sol - line 217
- File: contracts/VTVLVesting.sol - line 371
- File: contracts/VTVLVesting.sol - line 436
