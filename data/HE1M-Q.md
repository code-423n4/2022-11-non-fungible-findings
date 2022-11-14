### No. 1
The check for nonzero `recoveredSigner` is redundant, because if it is equal to address(0), `signer == recoveredSigner` on line 528 will return false (as we do not know the private key of address()).
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L525-L526