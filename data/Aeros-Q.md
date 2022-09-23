**Low Severity Findings:**


Ideally the codebase should be using Solidity version 0.8.7 (rather than 0.8.14) as it is a more stable, trusted version. All 4 of the contracts in scope use version 0.8.14:
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L2
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L2
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L2
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L2

This line compares the boolean value of a claim to a boolean constant, which is unnecessary.
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111
Original:     require(_claim.isActive *== true*, "NO_ACTIVE_CLAIM");
Fix:             require(_claim.isActive, "NO_ACTIVE_CLAIM");

Function withdrawAdmin() is declared public but is not used within the contract anywhere; it should be external.
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398
Original:    function withdrawAdmin(uint112 _amountRequested) *public* onlyAdmin {
Fix:            function withdrawAdmin(uint112 _amountRequested) *external* onlyAdmin {


---

**Non-Critical Findings:**


Misspellings/Typos:

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L168
Original:       // Next, we need to *calculated* the duration truncated to nearest releaseIntervalSecs
Fix:               // Next, we need to *calculate* the duration truncated to nearest releaseIntervalSecs

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L373
Original:      // Make sure we didn't already withdraw more *that* we're allowed.
Fix:              // Make sure we didn't already withdraw more *than* we're allowed.

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L390
Original:     // Let withdrawal known to everyone.
Fix:             // Let withdrawal *be* known to everyone.

Lines 40 and 41 of VTVLVesting.sol:
        // uint112 range: range 0 –     5,192,296,858,534,827,628,530,496,329,220,095.
        // uint112 range: range 0 –                             5,192,296,858,534,827.
This is a confusing. Perhaps the second line was not meant to be there? 2^112 -1 equals the top number, so the first line is accurate.
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L40
