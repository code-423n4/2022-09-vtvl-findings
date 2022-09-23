# 2022-09-VTVL

## Low Risk and Non-Critical Issues

### `public` functions not called by the contract should be declared `external` instead

_There are **2** instances of this issue:_

```solidity
File: contracts/AccessProtected.sol

39:   function setAdmin(address admin, bool isEnabled) public onlyAdmin {
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol

```solidity
File: contracts/VTVLVesting.sol

398:  function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol

### Non-library/interface files should use fixed compiler versions, not floating ones

_There are **1** instances of this issue:_

```solidity
File: contracts/token/VariableSupplyERC20Token.sol

2:    pragma solidity ^0.8.14;
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol

### File does not contain an SPDX identifier

_There are **4** instances of this issue:_

```solidity
File: contracts/AccessProtected.sol

      /// @audit
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol

```solidity
File: contracts/token/FullPremintERC20Token.sol

      /// @audit
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/FullPremintERC20Token.sol

```solidity
File: contracts/token/VariableSupplyERC20Token.sol

      /// @audit
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/VariableSupplyERC20Token.sol

```solidity
File: contracts/VTVLVesting.sol

      /// @audit
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol

### Natspec is missing

_There are **1** instances of this issue:_

```solidity
File: contracts/token/FullPremintERC20Token.sol

      /// @audit
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/token/FullPremintERC20Token.sol

### Event is missing `indexed` fields

_There are **1** instances of this issue:_

```solidity
File: contracts/VTVLVesting.sol

event      ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim)
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol

### Not used import

_There are **2** instances of this issue:_

```solidity
File: contracts/AccessProtected.sol

5:    import "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/AccessProtected.sol

```solidity
File: contracts/VTVLVesting.sol

6:    import "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/code-423n4/2022-09-vtvl/tree/main/contracts/VTVLVesting.sol
