Use custom errors for error messaging as they are cheaper than strings in requires.

In function calls parameters that define storage should be calldata instead of memory (since its cheaper)

353: For loop could be optimised by:
- changing i++ to ++i
- not initializing i
- using unchecked for ++i

