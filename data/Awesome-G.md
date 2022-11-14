## Remove Shorthand Addition/Subtraction Assignment

Instead of using the shorthand of addition/subtraction assignment operators (`+=`, `-=`)
it costs less to remove the shorthand (`x += y` same as `x = x+y`) saves ~22 gas

There are 6 instances of this issue:

[Line 316](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316), [Line 601](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L601), [Line 74](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L74), [Line 574](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574), [Line 46](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46), [Line 73](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73)


## Functions With Access Control Cheaper if Payable

A function with access control marked as payable will be cheaper for legitimate callers: the compiler removes checks for `msg.value`, saving approximately `20` gas per function call.

There are 2 instances of this issue:

[Line 56-66](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L56-L66), [Line 323-352](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L352)


## Caching `Storage` Variables in `Memory` To Save Gas

Anytime you are reading from `storage` more than once, it is cheaper in gas cost to cache the variable in `memory`: a `SLOAD` cost 100gas, while `MLOAD` and `MSTORE` cost 3 gas.

Gas savings: at least 97 gas.

There is 1 instance of this issue:

[Line 543-551](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L543-L551)


## Function Order Affects Gas Consumption

public/external function names and public member variable names can be optimized to save gas. See [this](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per sorted position shifted
