Total Low Severity: 2
Total Non-Critical Severity: 7


## (1) Update OpenZeppelin Packages to latest v4.7.3

Severity: Low

According to package.json, the OZ packages are currently set to ^4.5.0

## Proof of Concept

	"@openzeppelin/contracts": "^4.5.0",
Package.json#L7

## Recommended Mitigation Steps

Update the versions of @openzeppelin/contracts and @openzeppelin/contracts-upgradeable to be the latest in package.json. I also recommend double checking the versions of other dependencies as a precaution, as they may include important bug fixes.

## (2) Missing Checks for Address(0x0) 

Severity: Low

Missing zero address check for accidently calling revokeClaim on address(0).

## Proof Of Concept


	function revokeClaim(address _recipient) external onlyAdmin hasActiveClaim(_recipient) {
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L418



## Recommended Mitigation Steps

Consider adding zero-address checks in the mentioned codebase.


## (3) No need to check for true in require

Severity: Non-Critical

Since isActive is bool, there is no need to check for "true" value, can simply call:
require(_claim.isActive, "NO_ACTIVE_CLAIM");

## Proof Of Concept
	require(_claim.isActive == true, "NO_ACTIVE_CLAIM");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L111


## Recommended Mitigation Steps

	require(_claim.isActive, "NO_ACTIVE_CLAIM");

## (4) Avoid Floating Pragmas: The Version Should Be Locked

Severity: Non-Critical

## Proof Of Concept

	Found usage of floating pragmas ^0.8.14 of Solidity
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L2


## (5) Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

Severity: Non-Critical

## Proof Of Concept


	contract VTVLVesting is Context, AccessProtected 
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L11

	contract FullPremintERC20Token is ERC20 
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/FullPremintERC20Token.sol#L8

	contract VariableSupplyERC20Token is ERC20, AccessProtected 
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L10





## (6) Public Functions Not Called By The Contract Should Be Declared External Instead

Severity: Non-Critical

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

## Proof Of Concept


	function setAdmin(
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol#L39

	function withdrawAdmin(
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L398

	function mint(
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L36


## (7) Commented Code

Severity: Non-Critical

## Proof Of Concept

	// require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
    // require(_claim.endTimestamp > 0, "NO_END_TIMESTAMP");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L114-L115

	// require(_claim.isActive == false, "CLAIM_ALREADY_EXISTS");

    // Further checks aren't necessary (to save gas), as they're done at creation time (createClaim)
    // require(_claim.endTimestamp == 0, "CLAIM_ALREADY_EXISTS");
    // require(_claim.linearVestAmount + _claim.cliffAmount == 0, "CLAIM_ALREADY_EXISTS");
    // require(_claim.amountWithdrawn == 0, "CLAIM_ALREADY_EXISTS");
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L133-L138

	// uint constant _initialSupply = 100 * (10**18);
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/FullPremintERC20Token.sol#L9

## (8) TODOs

Severity: Non-Critical

## Proof Of Concept

	// Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L266


## (9) Non-usage of specific imports

Severity: Non-Critical

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files


## Proof of Concept
	import "./AccessProtected.sol";
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol#L9

	import "../AccessProtected.sol";
https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol#L5


## Recommended Mitigation Steps

Use specific imports syntax per solidity docs recommendation.
