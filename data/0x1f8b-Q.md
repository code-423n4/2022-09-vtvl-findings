- [Low](#low)
    - [**1. Outdated packages**](#1-outdated-packages)
    - [**2. Mixing and Outdated compiler**](#2-mixing-and-outdated-compiler)
    - [**3. Admin can mint any amount of tokens to an arbitrary address**](#3-admin-can-mint-any-amount-of-tokens-to-an-arbitrary-address)
    - [**4. Lack of ACK during owner change**](#4-lack-of-ack-during-owner-change)
    - [**5. Poor governance design**](#5-poor-governance-design)
- [Non critical](#non-critical)
    - [**6. Integer overflow by unsafe casting**](#6-integer-overflow-by-unsafe-casting)
    - [**7. Use tokens with ERC20 permit**](#7-use-tokens-with-erc20-permit)
    - [**8. Inheritance issues**](#8-inheritance-issues)
    - [**9. Open TODO**](#9-open-todo)

# Low

## **1. Outdated packages**

Some used packages are out of date, it is good practice to use the latest version of these packages:

```
    "@openzeppelin/contracts": "^4.5.0"
```

**Affected source code:**

- [package.json:7](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/package.json#L7)

## **2. Mixing and Outdated compiler**

The pragma version used are:

```
pragma solidity 0.8.14;
pragma solidity ^0.8.14;
```

*Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.16](https://github.com/ethereum/solidity/releases/tag/v0.8.16); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

Apart from these, there are several minor bug fixes and improvements.

## **3. Admin can mint any amount of tokens to an arbitrary address**

The owner can mint an arbitrary amount of tokens to an arbitrary address.

This is needless and poses a severe risk of centralization. A malicious or compromised owner can take advantage of this.

**Affected source code:**

- [VariableSupplyERC20Token.sol:36](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L36)

## **4. Lack of ACK during owner change**

It's possible to lose the ownership under specific circumstances.

Because of human error it's possible to set a new invalid owner. When you want to change the owner's address it's better to propose a new owner, and then accept this ownership with the new wallet.

**Affected source code:**

- [AccessProtected.sol:39](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39)

## **5. Poor governance design**

Instead of using a `TimeLock` or a multisignature wallet, an unlimited number of owners are allowed which can lead to governance issues.

**Affected source code:**

- [AccessProtected.sol](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol)

---

# Non critical

## **6. Integer overflow by unsafe casting**

Keep in mind that the version of solidity used, despite being greater than `0.8`, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

**Recommendation:**

Use a [safeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) from Open Zeppelin.

```javascript
uint40(block.timestamp)
```

**Affected source code:**

- [VTVLVesting.sol:217](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L217)
- [VTVLVesting.sol:371](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L371)
- [VTVLVesting.sol:436](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L436)

## **7. Use tokens with ERC20 permit**

The erc20 signature approval extension is a major usability enhancement that helps users and projects reap its benefits in the future.

The use of this feature is fully recommended.

**Reference:**

- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/draft-ERC20Permit.sol

**Affected source code:**

- [FullPremintERC20Token.sol:8](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L8)
- [VariableSupplyERC20Token.sol:10](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L10)

## **8. Inheritance issues**

You can see that the `VTVLVesting` contract inherits from `Context` and `AccessProtected`, however, `AccessProtected` already inherits from `Context` so the double definition is unnecessary and redundant.

**Affected source code:**

- [VTVLVesting.sol:11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L11)
- [AccessProtected.sol:11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L11)

## **9. Open TODO**

The code that contains "open todos" reflects that the development is not finished and that the code can change a posteriori, prior release, with or without audit.

**Affected source code:**

> // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?

- [VTVLVesting.sol:266](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266)

