ERC20 transfer and transferFrom: Should return a boolean. Several tokens do not return a boolean on these functions. As a result, their calls in the contract might fail. (See here: https://github.com/crytic/building-secure-contracts/blob/master/development-guidelines/token_integration.md#erc-conformity )

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L635
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L639
==========================================================
