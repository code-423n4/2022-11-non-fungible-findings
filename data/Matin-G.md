1 - uint8 is less efficient than uint256 in loop iterations:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598

2 - Caching array length in for loops can save gas:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598


3 - Pre-increments are cheaper than post-increments: (use ++i instead of +=1 & i++)

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/PolicyManager.sol#L77
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316


4 - Re-initialization of variables(uint256, uint8, bool) to their default value is redundant:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L597
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L147
