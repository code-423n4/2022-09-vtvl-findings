## [NAZ-G1] Pointless Casting That Wastes Gas
**Context**: [`VTVLVesting.sol#L436`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L436)

**Description**:
This imports are unneeded because they are imported from another contract.

**Recommendation**:
Consider removing the the extra imports.


## [NAZ-G2] Pointless Casting That Wastes Gas
**Context**: [`VTVLVesting.sol#L436`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L436)

**Description**:
The event `ClaimRevoked()` takes `uint256 revocationTimestamp` but when the event is emitted `uint40(block.timestamp)` is used, which does a pointless casting `uint256 -> uint40 -> uint256`.

**Recommendation**:
Consider just emitting `block.timestamp`.


## [NAZ-G3] Skip Initialization of Variable
**Context**: [`VTVLVesting.sol#L27`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L27)

**Description**:
It's cheaper to skip initialization of state variables to their default values.

**Recommendation**:
Consider skipping the initialization of these listed variable.


## [NAZ-G4] Use `++index` instead of `index++` to increment a loop counter
**Context**: [`VTVLVesting.sol#L353`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)

**Description**:
Due to reduced stack operations, using `++index` saves 5 gas per iteration.

**Recommendation**: 
Use `++index `to increment a loop counter.


## [NAZ-G5] The Increment In For Loop Post Condition Can Be Made Unchecked
**Context**: [`VTVLVesting.sol#L353`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353)

**Description**:
(This is only relevant if you are using the default solidity checked arithmetic). `i++` involves checked arithmetic, which is not required. This is because the value of `i` is always strictly less than length <= 2**256 - 1. Therefore, the theoretical maximum value of `i` to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. One can manually do this by:
```js
for (uint i = 0; i < length; ) {
    // do something that doesn't change the value of i
    unchecked {
        ++i;
    }
}
```

**Recommendation**: 
Consider doing the increment in the for loop post condition in an unchecked block.


## [NAZ-G6] In `require()`, Use `!= 0` Instead of `> 0` With Uint Values
**Context**: [`FullPremintERC20Token.sol#L11`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L11), [`VariableSupplyERC20Token.sol#L27`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L27), [`VariableSupplyERC20Token.sol#L31`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L31), [`VariableSupplyERC20Token.sol#L40`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L40), [`VTVLVesting.sol#L107`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L107), [`VTVLVesting.sol#L256-L257`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L256-L257), [`VTVLVesting.sol#L263`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L263), [`VTVLVesting.sol#L272-L273`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L272-L273), [`VTVLVesting.sol#L449`](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L449)

**Description**:
In a require, when checking a uint, using `!= 0` instead of `> 0` saves 6 gas. This will jump over or avoid an extra `ISZERO` opcode.

**Recommendation**: 
Use `!= 0` instead of `> 0` with uint values but only in `require()` statements.


## [NAZ-G] Setting The Constructor To Payable
**Context**: [`All Contracts`]()

**Description**:
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of `msg.value == 0` and saves 21 gas on deployment with no security risks.

**Recommendation**: 
Set the constructor to payable.


## [NAZ-G7] Use of Custom Errors Instead of String
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts)

**Description**:
To save some gas the use of custom errors leads to cheaper deploy time cost and run time cost. The run time cost is only relevant when the revert condition is met.

**Recommendation**: 
Use Custom Errors instead of strings.


## [NAZ-G8] Function Ordering via Method ID
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts)

**Description**:
Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. One could use [`This tool`](https://emn178.github.io/solidity-optimize-name/) to help find alternative function names with lower Method IDs while keeping the original name intact.

**Recommendation**: 
Find a lower method ID name for the most called functions for example ```mostCalled()``` vs. ```mostCalled_41q()``` is cheaper by 44 gas.