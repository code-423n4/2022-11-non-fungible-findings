## No Storage Gap for Upgradeable Contracts
Consider adding a storage gap at the end of the upgradeable contract, `Exchange.sol`, just in case it would entail some child contracts in the future. This would ensure no shifting down of storage in the inheritance chain. 

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

## Events Associated With Setter Functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value. Here are THE FOUR instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L356

## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L377-L378

