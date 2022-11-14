# GAS ISSUES FOR [2022-11-NON-FUNGIBLE](https://github.com/code-423n4/2022-11-non-fungible)

## [G-01] Inline internal functions to save gas

./contracts/Pool.sol

```
L70:   function _transfer(address from, address to, uint256 amount) private {
```

## [G-02] Use custom errors to save gas

./contracts/Pool.sol

```
L63:   revert('Caller is not authorized to transfer');
```

./contracts/Exchange.sol

```
L36:   require(isOpen == 1, "Closed");

L49:   require(isInternal, "This function should not be called directly");

L245:  require(_validateSignatures(sell, sellHash), "Sell failed authorization");

L248:  require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");

L249:  require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");

L295:  require(!cancelledOrFilled[hash], "Order already cancelled or filled");

L327:  require(address(_executionDelegate) != address(0), "Address cannot be zero");

L345:  require(_oracle != address(0), "Address cannot be zero");

L545:  require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

L549:  require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

L552:  require(canMatch, "Orders cannot be matched");

L632:  require(success, "ETH transfer failed");

L636:  require(success, "Pool transfer failed");

L641:  revert("Invalid payment token");
```

## [G-03] Use default values of variables types

Description: uint256 - 0;, string - "";, address - address(0);, etc.

./contracts/Exchange.sol

```
L146:  bool public isInternal = false;

L147:  uint256 public remainingETH = 0;

L184:  for (uint8 i = 0; i < executionsLength; i++) {

L307:  for (uint8 i = 0; i < orders.length; i++) {

L598:  for (uint8 i = 0; i < fees.length; i++) {
```

## [G-04] Use `++i` instead of `i++`

./contracts/Exchange.sol

```
L184:  for (uint8 i = 0; i < executionsLength; i++) {

L307:  for (uint8 i = 0; i < orders.length; i++) {

L598:  for (uint8 i = 0; i < fees.length; i++) {
```

## [G-05] Consider shortening some string to avoid extra gas

./contracts/Pool.sol

```
L63:   revert('Caller is not authorized to transfer');
```

./contracts/Exchange.sol

```
L49:   require(isInternal, "This function should not be called directly");

L295:  require(!cancelledOrFilled[hash], "Order already cancelled or filled");

L604:  require(totalFee <= price, "Total amount of fees are more than the price");
```

## [G-06] Make `constant` and `immutable` variables `private` instead of `public`

Description: Removes the getter function for this variable which saves gas. At the same time the variable get still be read from outside the contract

./contracts/Exchange.sol

```
L70:   string public constant NAME = "Exchange";

L72:   uint256 public constant INVERSE_BASIS_POINT = 10_000;

L73:   address public constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;

L74:   address public constant POOL = 0xF66CfDf074D2FFD6A4037be3A669Ed04380Aef2B;
```

## [G-07] `uncheck` the `i++`/`i--` in for loops since there's no way to overflow/underflow

./contracts/Exchange.sol

```
L184:  for (uint8 i = 0; i < executionsLength; i++) {

L307:  for (uint8 i = 0; i < orders.length; i++) {

L598:  for (uint8 i = 0; i < fees.length; i++) {
```
