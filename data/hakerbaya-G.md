Gas-Saving Patterns
The following are patterns you can make use of in your code to reduce gas consumption.

## Short-circuiting
Short-circuiting is a strategy we can make use of when an operation makes use of either || or &&. This pattern works by ordering the lower-cost operation first so that the higher-cost operation may be skipped (short-circuited) if the first operation evaluates to true.

```
// f(x) is low cost
// g(y) is expensive

// Ordering should go as follows
f(x) || g(y)
f(x) && g(y)
```

## Explicit function visibility
Explicit function visibility can often provide benefits in terms of smart contract security as well as gas optimization. For example, explicitly labeling external functions forces the function parameter storage location to be set as calldata, which saves gas each time the function is executed.

## Opaque predicate
The outcome of some conditions can be known without being executed and thus do not need to be evaluated.
```
function opaquePredicate(uint x) public pure {
  if(x > 1) {
    if(x > 0) {
      return x;
    }
  }
}
```
## Expensive operations in a loop
Due to the expensive SLOAD and SSTORE opcodes, managing a variable in storage is much more expensive than managing variables in memory. For this reason, storage variables should not be used in loops.

```
uint num = 0;

function expensiveLoop(uint x) public {
  for(uint i = 0; i < x; i++) {
    num += 1;
  }
}
```

## Constant outcome of a loop
If the outcome of a loop is a constant that can be inferred during compilation, it should not be used.

```
function constantOutcome() public pure returns(uint) {
  uint num = 0;
  for(uint i = 0; i < 100; i++) {
    num += 1;
  }
  return num;
}
```
## Loop fusion
Occasionally in smart contracts, you may find that there are two loops with the same parameters. In the case that the loop parameters are the same, there is no reason to have separate loops.

```
function loopFusion(uint x, uint y) public pure returns(uint) {
  for(uint i = 0; i < 100; i++) {
    x += 1;
  }
  for(uint i = 0; i < 100; i++) {
    y += 1;
  }
  return x + y;
}
```
## Comparison with unilateral outcome in a loop
When a comparison is executed in each iteration of a loop but the result is the same each time, it should be removed from the loop.

```
function unilateralOutcome(uint x) public returns(uint) {
  uint sum = 0;
  for(uint i = 0; i <= 100; i++) {
    if(x > 1) {
      sum += 1;
    }
  }
  return sum;
}
```
# ****************
# other tips  
# ****************

## Pack your variables!
In ethereum, you pay gas for every storage slot you use. A slot is of 256 bits, and you can pack as many variables as you want in it. Packing is done by solidity compiler and optimizer automatically, you just need to declare the packable functions consecutively.

The below code is an example of poor code and will consume 3 storage slot
```
uint8 numberOne;
uint256 bigNumber;
uint8 numberTwo;
```
A much more efficient way to do this in solidity will be
```
uint8 numberOne;
uint8 numberTwo;
uint256 bigNumber;
```
This small change will save you a lot of gas as it will now only need 2 slots to store (It takes 20k gas to store 1 slot of data). These small things are what make solidity different from other languages. Stuff like the order of variables never mattered as much in other languages when it came to optimization. Another thing to notice is that structs, mappings, and arrays always start from a new slot.

## uint8 is not always cheaper than uint256
The EVM only operates on 32 bytes/ 256 bits at a time. This means that if you use uint8, EVM has to first convert it uint256 to work on it and the conversion costs extra gas! You may wonder, What were the devs thinking? Why did they create smaller variables then? The answer lies in packing. In solidity, you can pack multiple small variables into one slot, but if you are defining a lone variable and can’t pack it, it’s optimal to use a uint256 rather than uint8.

## Mappings are cheaper than Arrays! Mostly
Solidity is the first language that I have used in which mappings are less expensive than arrays! This is because of how EVM works, An array is not stored sequentially in memory but as a mapping. You can pack Arrays but not Mappings though. So, it’s cheaper to use arrays if you are using smaller elements like uint8 which can be packed together. You can’t get the length of a mapping or parse through all its elements, so depending on your use case, you might be forced to use an Array even though it might cost you more gas.

## Not all elements can be packed.
Elements in Memory and Call Data cannot be packed. There is no gas saving in solidity by using smaller variables in function calls and memory.

## Use bytes32 rather than string/bytes.
If you can fit your data in 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is much cheaper in solidity. Basically, Any fixed size variable in solidity is cheaper than variable size.

## Make fewer external calls.
Every call to an external contract costs a decent amount of gas. For optimization of gas usage, It’s better to call one function and have it return all the data you need rather than calling a separate function for every piece of data. This might go against the best coding practices for other languages, but solidity is special.

## Use external function modifier.
For all the public functions, the input parameters are copied to memory automatically, and it costs gas. If your function is only called externally, then you should explicitly mark it as external. External function’s parameters are not copied into memory but are read from calldata directly. This small optimization in your solidity code can save you a lot of gas when the function input parameters are huge.

## Delete variables that you don’t need.
In Ethereum, you get a gas refund for freeing up storage space. If you don’t need a variable anymore, you should delete it using the delete keyword provided by solidity or by setting it to its default value. This will also help in keeping the blockchain size smaller. An ocean is filled with droplets.

## Use Short Circuiting rules to your advantage.
When using logical disjunction (||), logical conjunction (&&), make sure to order your functions correctly for optimal gas usage. In logical disjunction (OR), if the first function resolves to true, the second one won’t be executed and hence save you gas. In logical disjunction (AND), if the first function evaluates to false, the next function won’t be evaluated. Therefore, you should order your functions accordingly in your solidity code to reduce the probability of needing to evaluate the second function.
