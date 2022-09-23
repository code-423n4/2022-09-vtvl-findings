Low risks: 4

L-01. Input array lengths may differ

If the caller makes a copy-paste error, the lengths may be mismatchd and an operation believed to have been completed may not in fact have been completed

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L333-L341

L-02. _safeMint() should be used rather than _mint() wherever possible

_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. 
Use openzeppelin's safeMint() - https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L45
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L12

L-03. Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.
https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L11

L-04. Several critical operations do not trigger events, which will make it difficult to review the correct behavior of the contracts once deployed.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L446

Non-critical Issues: 4

N-01. Open Todos

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L266

N-02. Event is missing indexed fields

This events should use more indexed fields

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L64
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L69
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L74

N-03. Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents functions and change the visibility from external to public.
https://docs.soliditylang.org/en/latest/contracts.html#function-overriding

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39

N-04. Commented code

There are portions of commented code in the contracts.

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L114-L115
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L136-L138
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L261
