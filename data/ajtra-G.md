# Index
[G01]. Post-increment/decrement cost more gas then pre-increment/decrement
[G02]. Split require statement instead of &&
[G03]. I = I + (-) X is cheaper in gas cost than I += (-=) X
[G04]. Use custom errors rather than REVERT()/REQUIRE() strings to save deployment gas
[G05]. Don't compare boolean expressions to boolean literals
[G06]. Using bools for storage incurs overhead
[G07]. != 0 is cheaper than > 0 especially in state variables
[G08]. Should use unchecked in arithmetic operations when it's not possible to overflow
[G09]. Initialize variables with default values are not needed
[G10]. Cache into local variable instead of access to the storage
[G11]. Stack variable used as a cheaper cache for a state variable is only used once
[G12]. Functions guaranteed to revert when called by normal users can be marked payable
[G13]. Usage of uints/ints smaller than 32 Bytes (256 bits) incurs overhead

# Details
## [G01] Post-increment/decrement cost more gas then pre-increment/decrement
### Description
++I (--I) cost less gas than I++ (I--) especially in a loop.

### Proof of concept

```solidity
contract TestPost {
	function testPost() public {
		uint256 i;
		i++;
	}
}
contract TestPre {
	function testPre() public {
		uint256 i;
		++i;
	}
}
```

- Transaction cost of testPost is 21333 gas
- Transaction cost of testPre is 21328 gas 
- After the test it's possible to save **5 gas per ocurrence** with this optimization.

### Lines in the code
[VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)

## [G02]. Split require statement instead of &&
### Description
Split of conditions of an require sentence in different requires sentences save gas.

### Proof of concept
```diff
contract TestA {
    function Test (uint256 paramA) public {
        require(paramA > 1 && paramA > 2);
    }
}

contract TestB {
    function Test (uint256 paramA) public {
        require(paramA > 1);
        require(paramA > 2);
    }
}
```
- Transaction cost of TestA is 21642 gas
- Transaction cost of TestB is 21634 gas 
- After the test it's possible to save **8 gas per ocurrence** with this optimization.

### Lines in the code
[VTVLVesting.sol#L344-L349](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344-L349)
[VTVLVesting.sol#L270](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270)

## [G03]. I = I + (-) X is cheaper in gas cost than I += (-=) X
### Description
This is especially with state variables. In the following example I'm trying to demostrate 
the save gas in a loop of 10 iterations.

### Proof of concept
```diff
contract TestA {
    uint256 i;
    function Test () public {
        
        for(;i<10;){
            i += 1;
        }
    }

}

contract TestB {
    uint256 i;
    function Test () public {
        
        for(;i<10;){
            i = i + 1;
        }
    }
}
```
- Transaction cost of TestA is 48793 gas
- Transaction cost of TestB is 48663 gas
- After the test it's possible to save **130 gas (13 gas per iteration/ocurrence)** with this optimization.

### Lines in the code
[VariableSupplyERC20Token.sol#L43](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L43)
[VTVLVesting.sol#L161](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L161)
[VTVLVesting.sol#L179](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L179)
[VTVLVesting.sol#L301](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L301)
[VTVLVesting.sol#L381](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L381)
[VTVLVesting.sol#L383](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L383)
[VTVLVesting.sol#L433](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L433)

## [G04]. Use custom errors rather than REVERT()/REQUIRE() strings to save deployment gas
### Description
Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. 
Not defining the strings also save deployment gas.

### Lines in the code
[FullPremintERC20Token.sol#L11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11)
[VariableSupplyERC20Token.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27)
[VariableSupplyERC20Token.sol#L37](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L37)
[VariableSupplyERC20Token.sol#L41](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L41)
[AccessProtected.sol#L25](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L25)
[AccessProtected.sol#L40](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L40)
[VTVLVesting.sol#L82](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L82)
[VTVLVesting.sol#L107](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107)
[VTVLVesting.sol#L111](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111)
[VTVLVesting.sol#L129](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L129)
[VTVLVesting.sol#L255](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L255)
[VTVLVesting.sol#L256](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256)
[VTVLVesting.sol#L257](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L257)
[VTVLVesting.sol#L262](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L262)
[VTVLVesting.sol#L263](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263)
[VTVLVesting.sol#L264](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L264)
[VTVLVesting.sol#L270](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L270)
[VTVLVesting.sol#L295](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L295)
[VTVLVesting.sol#L344](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344)
[VTVLVesting.sol#L374](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L374)
[VTVLVesting.sol#L402](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L402)
[VTVLVesting.sol#L426](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L426)
[VTVLVesting.sol#L447](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L447)
[VTVLVesting.sol#L449](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449)

## [G05]. Don't compare boolean expressions to boolean literals
### Description
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

### Proof of concept
```diff
contract TestA {
    function Test (bool paramA) public {
        require(paramA == true, "Test bool");
    }
}

contract TestB {
    function Test (bool paramA) public {
        require(paramA, "Test bool");
    }
}
```
- Transaction cost of TestA is 21629 gas
- Transaction cost of TestB is 21611 gas
- After the test it's possible to save **18 gas per ocurrence** with this optimization.

### Lines in the code
[VTVLVesting.sol#L111](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L111)

## [G06]. Using bools for storage incurs overhead
### Description
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) 
when changing from 'false' to 'true', after having been 'true' in the past.

### Lines in the code
[AccessProtected.sol#L12](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L12)
[VTVLVesting.sol#L45](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L45)

## [G07]. != 0 is cheaper than > 0 especially in state variables
### Description
In cases where we used a variable from state assigned to a local variable it's the same gas save.

### Proof of concept
```solidity
contract TestA {
    uint256 _paramA;

    function Test () public returns (bool) {
        return _paramA > 0;
    }

}

contract TestB {
    uint256 _paramA;

    function Test () public returns (bool) {
        return _paramA != 0;
    }

}

contract TestC {
    uint256 _paramA;

    function Test () public returns (bool) {
        uint256 aux = _paramA;
        return aux > 0;
    }

}

contract TestD {
    uint256 _paramA;

    function Test () public returns (bool) {
        uint256 aux = _paramA;
        return aux != 0;
    }

}
```

- Transaction cost of TestA/C is 23491/23504 gas
- Transaction cost of TestB/D is 23494/23507 gas 
- After the test it's possible to save **3 gas per ocurrence** with this optimization.

### Lines in the code
[FullPremintERC20Token.sol#L11](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L11)
[VariableSupplyERC20Token.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L27)
[VariableSupplyERC20Token.sol#L31](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L31)
[VariableSupplyERC20Token.sol#L40](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L40)
[VTVLVesting.sol#L107](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L107)
[VTVLVesting.sol#L256](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L256)
[VTVLVesting.sol#L257](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L257)
[VTVLVesting.sol#L263](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L263)
[VTVLVesting.sol#L272](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L272)
[VTVLVesting.sol#L273](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L273)
[VTVLVesting.sol#L449](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L449)
	

## [G08] Should use unchecked in arithmetic operations when it's not possible to overflow
### Description
This situation ocurrs especially in loops

Example,

```diff
-	for (uint256 i = 0; i < length; i++) {
+	for (uint256 i = 0; i < length;) {
		_createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
+		unchecked {
+			i++;
+		}
	}
```

### Lines in the code
[VTVLVesting.sol#L292](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L292)
[VTVLVesting.sol#L301](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L301)
[VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)
[VTVLVesting.sol#L377](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L377)
[VTVLVesting.sol#L429](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L429)
[VariableSupplyERC20Token.sol#L43](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L43)

## [G09] Initialize variables with default values are not needed
### Description
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address,...). 
Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### Lines in the code
[VTVLVesting.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27)
[VTVLVesting.sol#L148](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148)
[VTVLVesting.sol#L353](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353)

## [G10] Cache into local variable instead of access to the storage
When it's going to access to a storage variable more than once it's a best practice 
to use a local variable to save gas.

### VariableSupplyERC20Token.mint
In this case the function it's accessing to the storage variable mintableSupply twice. It's possible 
to store the value in a local variable and then use it.

```diff
    function mint(address account, uint256 amount) public onlyAdmin {
        require(account != address(0), "INVALID_ADDRESS");
        // If we're using maxSupply, we need to make sure we respect it
        // mintableSupply = 0 means mint at will
+		uint256 _mintableSupply = mintableSupply;
-       if(mintableSupply > 0) {
-           require(amount <= mintableSupply, "INVALID_AMOUNT");
+       if(_mintableSupply > 0) {
+           require(amount <= _mintableSupply, "INVALID_AMOUNT");
            // We need to reduce the amount only if we're using the limit, if not just leave it be
            mintableSupply -= amount;
        }
        _mint(account, amount);
    }
```
### Lines in the code
[VariableSupplyERC20Token.sol#L36-L46](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L36-L46)

## [G11] Stack variable used as a cheaper cache for a state variable is only used once
### Description
If the variable is only accessed once, it's cheaper to use the state variable directly that one time, 
and **save the 3 gas** the extra stack assignment would spend

### Lines in the code
[VTVLVesting.sol#L124](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L124)
[VTVLVesting.sol#L197](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L197)
[VTVLVesting.sol#L400](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L400)

## [G12] Functions guaranteed to revert when called by normal users can be marked payable
### Description
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. 
Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include 
checks for whether a payment was provided. 
The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), 
which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Lines in the code
#### VariableSupplyERC20Token.mint
[VariableSupplyERC20Token.sol#L36](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L36)
#### AccessProtected.setAdmin
[AccessProtected.sol#L39](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39)
#### VTVLVesting.createClaim
[VTVLVesting.sol#L317](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L317)
#### VTVLVesting.createClaimsBatch
[VTVLVesting.sol#L333](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L333)
#### VTVLVesting.withdraw
[VTVLVesting.sol#L364](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L364)
#### VTVLVesting.withdrawAdmin
[VTVLVesting.sol#L398](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398)
#### VTVLVesting.revokeClaim
[VTVLVesting.sol#L418](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L418)
#### VTVLVesting.withdrawOtherToken
[VTVLVesting.sol#L446](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L446)

## [G13] Usage of uints/ints smaller than 32 Bytes (256 bits) incurs overhead
### Description
When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. 
This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, 
the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Use a larger size then downcast where needed

### Lines in the code
[VTVLVesting.sol#L27](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27)
[VTVLVesting.sol#L35](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L35)
[VTVLVesting.sol#L36](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L36)
[VTVLVesting.sol#L37](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L37)
[VTVLVesting.sol#L38](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L38)
[VTVLVesting.sol#L42](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L42)
[VTVLVesting.sol#L43](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L43)
[VTVLVesting.sol#L44](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L44)
[VTVLVesting.sol#L148](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L148)
[VTVLVesting.sol#L167](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L167)
[VTVLVesting.sol#L169](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L169)
[VTVLVesting.sol#L170](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L170)
[VTVLVesting.sol#L176](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L176)
[VTVLVesting.sol#L292](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L292)
[VTVLVesting.sol#L371](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L371)
[VTVLVesting.sol#L377](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L377)
[VTVLVesting.sol#L422](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L422)
[VTVLVesting.sol#L429](https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L429)
	