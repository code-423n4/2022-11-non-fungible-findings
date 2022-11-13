## Gas: Remove extraneous computation from `OrdersMatched` event emission

[Location](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L274-L275)

Unless there is a compelling business case for recording the maker/taker relationship of a trade, removing the computation for who was the maker and taker would save nearly 500 gas on a protocol hotpath. The effect is compounded if a bulk order is the source of the calls to `_execute()`

Consider instead simply recording the seller and buyer addresses. Since the inputs to `_execute` already contain the `listingTime` via the `Order` struct, the information can be computed offchain rather than forcing users to pay for the computation.