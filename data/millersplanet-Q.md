# Informational

- The contract `VariableSupplyERC20Token` does not have a locked pragma. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

- The following comment on `VTVLVesting.sol` does not take into consideration [the overflow of the UNIX epoch time that will occur in 2038](https://betterprogramming.pub/the-end-of-unix-time-what-will-happen-1b1a25ec1c20):

      `Gives us a range from 1 Jan 1970 (Unix epoch) up to approximately 35 thousand years from then (2^40 / (365 * 24 * 60 * 60) ~= 35k)`

  Not that this will affect the protocol soon but it's important to be aware of this information.
