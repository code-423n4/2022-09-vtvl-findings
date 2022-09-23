# 1 USE OF BLOCK.TIMESTAMP
 A miner can manipulate the block timestamp which can be used to their advantage to attack a smart contract via Block Timestamp Manipulation
block.timestamp is being used several times
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L217 
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L371

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

## Recommended Mitigation Steps

USE A MORE RECENT VERSION OF SOLIDITY and OpenZeppelin Library
current solidity version is 0.8.15 meanwhile project is using 0.8.6.
OpenZeppelin library currently 4.7.2 while the project is using 4.4.2.
It's best to use latest version because there are some features improvement and security patch.

Use block.number instead of  block.timestamp or now to reduce the risk of
MEV attacks

### here some reference :
https://www.bookstack.cn/read/ethereumbook-en/spilt.14.c2a6b48ca6e1e33c.md
https://ethereum.stackexchange.com/questions/108033/what-do-i-need-to-be-careful-about-when-using-block-timestamp
# 2  USE SAFETRANSFER/SAFETRANSFERFROM CONSISTENTLY INSTEAD OF TRANSFER/TRANSFERFROM
It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L388
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L407
# 3 _SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver
https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/token/VariableSupplyERC20Token.sol#L45