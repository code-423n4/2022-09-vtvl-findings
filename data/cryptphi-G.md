1.
The use of _msgSender() when there is no implementation of a meta transaction mechanism that uses it, such as EIP-2771, very slightly increases gas consumption. Currently, there is no provision for EIP-2271 implemented.

Replace _msgSender() with msg.sender if there is no mechanism to support meta-transactions like EIP-2771 implemented.