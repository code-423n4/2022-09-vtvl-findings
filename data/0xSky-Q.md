## There can be no admin

In setAdmin of AccessProtected.sol, when disable an admin, there is no check about how many admins left. 

https://github.com/code-423n4/2022-09-vtvl/blob/f68b7f3e61dad0d873b5b5a1e8126b839afeab5f/contracts/AccessProtected.sol#L39-L43

So it is possible that there is no admin in the Vesting contract. The impact is not huge because only admins can change admin status, and users can withdraw their claim without any admins. But in the situation, it is not possible to create new claims anymore, and revokeClaim is impossible when needed. So I raise this as a low risk.