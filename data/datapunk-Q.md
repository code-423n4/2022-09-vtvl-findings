
## Impact
use external instead of public to save gas, since no internal calls

## Proof of Concept
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39

## Tools Used

## Recommended Mitigation Steps
```function setAdmin(address admin, bool isEnabled) external onlyAdmin ```




## Impact
wrong comment. should be 40 bits instead of 48 bits

## Proof of Concept
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L370

## Tools Used

## Recommended Mitigation Steps
change 48 to 40


## Impact
does it prevent VTVL contract itself from becoming a recipient

## Proof of Concept
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255

## Tools Used

## Recommended Mitigation Steps
add `require(_recipient!=address(this));`


## Impact
use memory for usrClaim along with using _baseVestedAmount instead of vestedAmount will save gas

## Proof of Concept
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L367

## Tools Used

## Recommended Mitigation Steps
```
Claim memory usrClaim = claims[_msgSender()]; // @audit use memory
uint112 allowance = _baseVestedAmount(usrClaim, uint40(block.timestamp));
...
claims[_msgSender()] = usrClaim;
```


## Impact
use memory for _claim along with using _baseVestedAmount instead of vestedAmount will save gas

## Proof of Concept
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L418

## Tools Used

## Recommended Mitigation Steps
```
Claim memory _claim = claims[_recipient];
// Calculate what the claim should finally vest to
uint112 finalVestAmt = _baseVestedAmount(_claim, _claim.endTimestamp);
...
claims[_msgSender()] = _claim;
```

