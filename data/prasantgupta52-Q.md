# 1. _safeMint() should be used rather than _mint() wherever possible
### Description:
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.
### Links to github files
[FullPremintERC20Token.sol:L12](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L12)
[VariableSupplyERC20Token.sol:L45](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L45)
### Instances
```
contracts/token/FullPremintERC20Token.sol:12:        _mint(_msgSender(), supply_);
contracts/token/VariableSupplyERC20Token.sol:45:        _mint(account, amount);

```
### Recommendations:
Use _safeMint() instead of _mint().

----
# 2. Avoid using Floating Pragma:
### Description:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
### Links to github files
[VariableSupplyERC20Token.sol:L2](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L2)
### Instances
```
contracts/token/VariableSupplyERC20Token.sol:2:pragma solidity ^0.8.14;
```
----
# 3. Use of Block.timestamp
Impact - Non-Critical
### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
### Links to github files
[VTVLVesting.sol:L217](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L217)
[VTVLVesting.sol:L371](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L371)
[VTVLVesting.sol:L436](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L436)
### Instances
```
contracts/VTVLVesting.sol:217:        return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
contracts/VTVLVesting.sol:371:        uint112 allowance = vestedAmount(_msgSender(), uint40(block.timestamp));
contracts/VTVLVesting.sol:436:        emit ClaimRevoked(_recipient, amountRemaining, uint40(block.timestamp), _claim);
```
### Recommended Mitigation Steps

Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

----
# 4. Event is missing `indexed` fields
Impact - Non-Critical
Each `event` should use three `indexed` fields if there are three or more fields
### Links to github files
[VTVLVesting.sol:L69](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L69)
### Instances
```
contracts/VTVLVesting.sol:69:    event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
```
----
# 5. Variable names that consist of all capital letters should be reserved for const/immutable variables
### Description
If the variable needs to be different based on which class it comes from, a `view`/`pure` function should be used instead
### Links to github files
[VTVLVesting.sol:L17](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L17)
### Instances
```
contracts/VTVLVesting.sol:17:    IERC20 public immutable tokenAddress;
```
----
# 6. Open TODOs
### Description
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
### Links to github files
[VTVLVesting.sol:L266](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266)
### Instances
```
contracts/VTVLVesting.sol:266:        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
```
----