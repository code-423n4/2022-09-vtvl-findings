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