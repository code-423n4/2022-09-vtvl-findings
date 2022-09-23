### Non-Critical Issues List
| Number |Issues Details|
|:--:|:-------|
|[N-01]|Inconsistent Solidity Versions|
|[N-02]|Use a more recent version of solidity|
|[N-03]|Function writing that does not comply with the 'Solidity Style Guide’|
|[N-04]|Long lines are not suitable for the 'Solidity Style Guide'|

Total 4 issues


### Low Risk Issues List
| Number |Issues Details|
|:--:|:-------|
|[L-01]|Floating pragma|
|[L-02]|Add to blacklist function|

Total 2 issues

## [N-01] Inconsistent solidity versions

**Description:**
Different Solidity compiler versions are used throughout ```contracts/token repositories```. The following contracts mix versions:
 
pragma 0.8.14
pragma ^0.8.14

**Recommendation:**
Versions must be consistent with each other

**Full List:**
```js
contracts\AccessProtected.sol:
2: pragma solidity 0.8.14;

contracts\VTVLVesting.sol:
2: pragma solidity 0.8.14;

contracts\token\FullPremintERC20Token.sol:
2: pragma solidity 0.8.14;

contracts\token\VariableSupplyERC20Token.sol:
2: pragma solidity ^0.8.14;
```

## [N-02] Use a more recent version of solidity

**Context:**
[AccessProtected.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L2)
[VTVLVesting.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L2)
[FullPremintERC20Token.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/FullPremintERC20Token.sol#L2)
[VariableSupplyERC20Token.sol#L2](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L2)

**Description:**
Old version of Solidity is used (```0.8.14```), newer version should be used

## [N-03] Function writing that does not comply with the 'Solidity Style Guide’

**Context:**
[VTVLVesting.sol](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol)

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.15/style-guide.html

Functions should be grouped according to their visibility and ordered:

- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

## [N-04] Long lines are not suitable for the ‘Solidity Style Guide’

**Context:**
[VariableSupplyERC20Token.sol#L21](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L21)
[VTVLVesting.sol#L169](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L169)
[VTVLVesting.sol#L326](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L326)
[VTVLVesting.sol#L354](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L354)

**Description:**
It is generally recommended that lines in the source code should not exceed 80-120 characters. Today's screens are much larger, so in some cases it makes sense to expand that. The lines above should be split when they reach that length, as the files will most likely be on GitHub and GitHub always uses a scrollbar when the length is more than 164 characters.

(why-is-80-characters-the-standard-limit-for-code-width)[https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width]


**Recommendation:**
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction

```js
thisFunctionCallIsReallyLong(
    longArgument1,
    longArgument2,
    longArgument3
);
```

## [L-01]  Floating Pragma

**Description:**
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
https://swcregistry.io/docs/SWC-103

Floating Pragma List: 
pragma ^0.8.14.  (contracts/token/VariableSupplyERC20Token.sol)

**Recommendation:**
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

## [L-02] Add to Blacklist function

**Description:**
Cryptocurrency mixing service, Tornado Cash, has been blacklisted in the OFAC.
A lot of blockchain companies, token projects, NFT Projects have ```blacklisted``` all Ethereum addresses owned by Tornado Cash listed in the US Treasury Department's sanction against the protocol.
https://home.treasury.gov/policy-issues/financial-sanctions/recent-actions/20220808
In addition, these platforms even ban accounts that have received ETH on their account with Tornadocash.

Some of these Projects;
* USDC (https://www.circle.com/en/usdc)
* Flashbots (https://www.paradigm.xyz/portfolio/flashbots )
* Aave (https://aave.com/)
* Uniswap
* Balancer
* Infura
* Alchemy 
* Opensea
* dYdX 

Details on the subject;
https://twitter.com/bantg/status/1556712790894706688?s=20&t=HUTDTeLikUr6Dv9JdMF7AA

https://cryptopotato.com/defi-protocol-aave-bans-justin-sun-after-he-randomly-received-0-1-eth-from-tornado-cash/

For this reason, every project in the Ethereum network must have a blacklist function, this is a good method to avoid legal problems in the future, apart from the current need.

Transactions from the project by an account funded by Tonadocash or banned by OFAC can lead to legal problems.Especially American citizens may want to get addresses to the blacklist legally, this is not an obligation

If you think that such legal prohibitions should be made directly by validators, this may not be possible:
https://www.paradigm.xyz/2022/09/base-layer-neutrality

```The ban on Tornado Cash makes little sense, because in the end, no one can prevent people from using other mixer smart contracts, or forking the existing ones. It neither hinders cybercrime, nor privacy.```

Here is the most beautiful and close to the project example; Manifold

Manifold Contract
https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```js
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```
Recommended Mitigation Steps add to Blacklist function and modifier


## Suggestions
## [S-01] Remove TODO

**Context:**
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L266
[VTVLVesting.sol#L266](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L266)

**Description:**
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment


## [S-02] Remove to unused imports

**Description:**

[AccessProtected.sol#L5](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/AccessProtected.sol#L5)
[VTVLVesting.sol#L6](https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L6)


Recommendation:
Remove to unused imports from AccessProtected.sol and VTVLVesting.sol

