# [01] The increment in for loop post condition can be made unchecked to save gas

It can save 30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked).

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598

# [02] x += y costs more gas than x = x + y for state variables

Using the addition operator instead of plus-equals saves 113 [gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8).

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L36

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46

# [03] Repeated statements used for validation can be converted into a function modifier to save gas

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L327

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L336

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L345
