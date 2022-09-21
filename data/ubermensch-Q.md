## Approve Race Condition

### Impact
A spender can see the token owner broadcast a transaction changing their approval, and then quickly sign and broadcast a transaction using `transferFrom` to move the currently approved amount from the owner's balance to the spender. This is known as a racing condition and is present in the standard `ERC20` implementation. The spender will be able to receive both approval amounts for both transactions if their validation occurs before the approver's.

### Proof of Concept
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/FullPremintERC20Token.sol#L8
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/token/VariableSupplyERC20Token.sol#L10

### Recommended Mitigation Steps

Instead of changing the approval amount using the `approve` function, consider using the `increaseAllowance` and `decreaseAllowance` functions.



## The Contract Can End up With No Admins
### Impact
The `setAdmin` function is used to add or remove admins from the contract, there is a possibility that admins disables all the admins by mistake which will result in a denial of service in all the functionalities that requires the admin interaction.

### Proof of Concept
https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39-L43

### Recommended Mitigation Steps
It is recommended to add a restriction that will revert the transaction if the `setAdmin` call is going to remove all the admins of the contract.
