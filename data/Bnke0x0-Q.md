








### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::83 => tokenAddress = _tokenAddress;
2022-09-vtvl/contracts/VTVLVesting.sol::106 => Claim storage _claim = claims[_recipient];
2022-09-vtvl/contracts/VTVLVesting.sol::155 => _referenceTs = _claim.endTimestamp;
```



### [L02] `_safeMint()` should be used rather than `_mint()` wherever possible

#### Impact
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function

#### Findings:
```
2022-09-vtvl/contracts/token/FullPremintERC20Token.sol::12 => _mint(_msgSender(), supply_);
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::32 => mint(_msgSender(), initialSupply_);
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::45 => _mint(account, amount);
```

### [L03] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

    While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.
    
    A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.
    
    It is recommended to pin to a concrete compiler version.
#### Findings:
```
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::2 => pragma solidity ^0.8.14;
```





### [L04] Do not use Deprecated Library Functions

#### Impact
The usage of deprecated library functions should be discouraged.

This issue is mostly related to OpenZeppelin libraries.
#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::12 => using SafeERC20 for IERC20;
```



#### Non-Critical Issues

### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2022-09-vtvl/contracts/AccessProtected.sol::30 => return _admins[_addressToCheck];
2022-09-vtvl/contracts/VTVLVesting.sol::92 => return claims[_recipient];
2022-09-vtvl/contracts/VTVLVesting.sol::187 => return (vestAmt > _claim.amountWithdrawn) ? vestAmt : _claim.amountWithdrawn;
2022-09-vtvl/contracts/VTVLVesting.sol::198 => return _baseVestedAmount(_claim, _referenceTs);
2022-09-vtvl/contracts/VTVLVesting.sol::208 => return _baseVestedAmount(_claim, _claim.endTimestamp);
2022-09-vtvl/contracts/VTVLVesting.sol::217 => return _baseVestedAmount(_claim, uint40(block.timestamp)) - _claim.amountWithdrawn;
2022-09-vtvl/contracts/VTVLVesting.sol::224 => return vestingRecipients;
2022-09-vtvl/contracts/VTVLVesting.sol::231 => return vestingRecipients.length;
```






### [N02] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2022-09-vtvl/contracts/AccessProtected.sol::14 => event AdminAccessSet(address indexed _admin, bool _enabled);
2022-09-vtvl/contracts/VTVLVesting.sol::59 => event ClaimCreated(address indexed _recipient, Claim _claim);
2022-09-vtvl/contracts/VTVLVesting.sol::64 => event Claimed(address indexed _recipient, uint112 _withdrawalAmount);
2022-09-vtvl/contracts/VTVLVesting.sol::69 => event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);
2022-09-vtvl/contracts/VTVLVesting.sol::74 => event AdminWithdrawn(address indexed _recipient, uint112 _amountRequested);
```



### [N03] Use of sensitive/NC-inclusive terms


#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::45 => bool isActive; // whether this claim is active (or revoked)
```



### [N04] Open TODOs

#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
#### Findings:
```
2022-09-vtvl/contracts/VTVLVesting.sol::266 => // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
```




### [N05] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from external to public.
#### Findings:
```
2022-09-vtvl/contracts/AccessProtected.sol::39 => function setAdmin(address admin, bool isEnabled) public onlyAdmin {
2022-09-vtvl/contracts/VTVLVesting.sol::196 => function vestedAmount(address _recipient, uint40 _referenceTs) public view returns (uint112) {
2022-09-vtvl/contracts/VTVLVesting.sol::206 => function finalVestedAmount(address _recipient) public view returns (uint112) {
2022-09-vtvl/contracts/VTVLVesting.sol::398 => function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::36 => function mint(address account, uint256 amount) public onlyAdmin {
```

### [N06] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:

```
2022-09-vtvl/contracts/token/VariableSupplyERC20Token.sol::2 => pragma solidity ^0.8.14;
```





