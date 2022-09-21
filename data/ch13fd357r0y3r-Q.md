The `withdrawOtherToken()` is an admin function which is used to send tokens to users who accidentally sent other tokens to the contract.

If the admin calls this function the funds are directly sent to Admin EOA or Contract. Can add argument `address recipient to the `withdrawOtherToken()` function and send funds to the recipient who lost the funds.

Fix:

```
    function withdrawOtherToken(IERC20 _otherTokenAddress, address recipient) external onlyAdmin {
        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
        uint256 bal = _otherTokenAddress.balanceOf(address(this));
        require(bal > 0, "INSUFFICIENT_BALANCE");
        _otherTokenAddress.safeTransfer(recipient, bal);
    }
```