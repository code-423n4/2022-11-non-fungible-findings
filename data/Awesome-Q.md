## block.timestamp is unreliable

Using block.timestamp as part of the time checks could be slightly modified by miners/validators to favor them in contracts that contain logic heavily dependent on them.

Consider this problem and warn users that a scenario like this could occur. If the change of timestamps will not affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

There is 1 instance of this issue:

[Line 377-378](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L377-L378)

## Add Require Error Messages
Consider implementing a less than 32 character string message to all require statements to make it easier to debug.

There are 3 instances of this issue:

[Line 240](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L240), [Line 291](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L291), [Line 573](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573)

```
Line 240:    require(sell.order.side == Side.Sell);

Line 291:    require(msg.sender == order.trader);

Line 573:    require(remainingETH >= price);
```