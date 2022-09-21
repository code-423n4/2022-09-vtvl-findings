
   
## function can be marked as external instead of public


https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39


```
    function setAdmin(address admin, bool isEnabled) public onlyAdmin {
```
            

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L398


```
    function withdrawAdmin(uint112 _amountRequested) public onlyAdmin {    
```
            

## Splitting require() statements that use && saves gas

https://github.com/code-423n4/2022-01-xdefi-findings/issues/128

See this issue which describes the fact 
that there is a larger deployment gas cost, 
but with enough runtime calls, the change ends up being cheaper


https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L344


```
        require(_startTimestamps.length == length &&
```
            

## ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT�S USED IN FOR-LOOPS (--I/I-- TOO)
    
Saves 6 gas per loop


https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353


```
        for (uint256 i = 0; i < length; i++) {
```
            

## IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED


https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L353


```
        for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/VTVLVesting.sol#L27

```
    uint112 public numTokensReservedForVesting = 0;
```
    
## Use Customized error and revert instead of require to save gas

https://ethereum.stackexchange.com/questions/101782/requirecondition-message-vs-revert-with-a-custom-error-which-is-better-a

