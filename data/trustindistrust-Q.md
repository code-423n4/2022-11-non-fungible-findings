## Non-critical

### Events are confusingly named

[Location](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L23)

#### Description

The `Pool` contract uses events to log deposits and withdrawals. However, it uses the same event for both deposits and withdrawals. This makes log tracking on-chain harder to understand.

#### Suggested course of action

Create specific `Deposit` and `Withdraw` events for those actions.

## Non-criticial

### Events use incorrect value for parameter

Locations:
[1](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L37)
[2](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L49)

#### Description

The `Pool` contract uses events to log deposits and withdrawals. However, the code inserts the 0 address instead of the address of the contract, confusing the output.

#### Suggested course of action

Replace `address(0)` with `address(this)`.

## Non-Critical

### Pool contract should not be upgradable.

#### Description

The `Pool` contract acts as a source of ETH funds for use in executing purchases on the `Exchange`. Users may deposit ETH and use that ETH for purchases, or withdraw it.

However, the contract is upgradable for no clear reason. It's simple in form and purpose. Since it holds user's ETH directly, it should be immutable and unable to defraud users later, either maliciously or by accident of management.

#### Suggested course of action

Remove all upgradable features from the Pool contract.

## Non-critical: Move fee accumulator above the transfer of tokens in `_transferFees`

[Location](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L601)

While there is no risk of malicious effects, it is best practice to move changes to state or scoped state above side effects like transfers ("Checks Effects Interactions" pattern).

## Non-critical: Remove `return` statement from `_canMatchOrders` as it is unnecessary

[Location](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L554)

`_canMatchOrders` uses named return values, and contains a branch that ensures that the named return values can't be shadowed. As such, the final `return` statement is unnecessary and can be removed.

## Low: Sellers can grief by setting large Fee arrays containing `0` values, causing `execute` to consume more gas than expected to process trades.

[Location](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L598-L602)

#### Description

When a sell order is created, it includes provision for a dynamic `Fee` array. This struct is designed to allow the seller to indicate the amount and destination for fees owed during a trade.

The loop that processes the trades in `Exchange._transferFees()` has a cap of 255 such fee entries. As such, a seller that wanted to grief buyers could create and sign a sell `Order` with an array of at least 256 members with the `rate` set to 0. While this would still result in a sum of 0 for fees, the dapp itself is unlikely to indicate the higher cost of gas to perform the trade.

#### Suggested course of action

If possible, modify the `Order` struct to contain a sized `Fee` array that is set to a reasonable value based on business case.

If that isn't possible, insert a check for `0` at line 599 that `continue`s if the value is 0, thus reducing the gas impact of such an attack.