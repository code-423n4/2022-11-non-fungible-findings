[L-01]  '_execute()' function miss 'side' check for buy order
```
function _execute(Input calldata sell, Input calldata buy)
    // ...
{
    require(sell.order.side == Side.Sell);
    // @audit miss check for buy

    // ...
}
```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L240

[N-01] Declaration  of 'bulkExecute()' function has not been added to 'IExchange.sol'
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/interfaces/IExchange.sol#L36

