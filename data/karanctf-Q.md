## Low
## [L-1] Open TODOs
```solidity
VTVLVesting.sol:266:        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
```
## [L-2] Missing checks for address(0x0) when assigning values to address state variables filter for non address vairable
```solidity
VTVLVesting.sol:83:        tokenAddress = _tokenAddress;
```

## Non Critical
## [N-1] Try making public function internal if there is no use in other contracts like division multiply round etc
```solidity
VTVLVesting.sol:206:    function finalVestedAmount(address _recipient) public view returns (uint112) {
VTVLVesting.sol:398:    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    
```

## [N-2] Use scientific notation (e.g. `10e18`) rather than exponentiation (e.g. `10**18`)
```solidity
token/FullPremintERC20Token.sol:9:    // uint constant _initialSupply = 100 * (10**18);
test/TestERC20Token.sol:7:    // uint constant _initialSupply = 100 * (10**18);
test/TestERC20Token.sol:9:        uint256 supplyToMint = initialSupply_ * (10 ** decimals());
```

