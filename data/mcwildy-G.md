# GAS OPTIMIZATIONS REPORT

## NON-FUNGIBLE

### Use `uint256` instead of the smaller uints where possible

The word-size in ethereum is 32 bytes which means that every variables which is sized with less bytes will cost more to operate with

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol):

```solidity
line#L184: for (uint8 i = 0; i < executionsLength; i++) {

line#L307: for (uint8 i = 0; i < orders.length; i++) {

line#L479: uint8 v; bytes32 r; bytes32 s;

line#L519: uint8 v,
```

### Use custom errors instead of `require` strings

[Pool.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol):

```solidity
line#L63:  revert('Caller is not authorized to transfer');
```

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol):

```solidity
line#L36:  require(isOpen == 1, "Closed");

line#L49:  require(isInternal, "This function should not be called directly");

line#L246: require(_validateSignatures(buy, buyHash), "Buy failed authorization");

line#L248: require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");

line#L295: require(!cancelledOrFilled[hash], "Order already cancelled or filled");

line#L327: require(address(_executionDelegate) != address(0), "Address cannot be zero");

line#L345: require(_oracle != address(0), "Address cannot be zero");

line#L414: require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

line#L545: require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

line#L549: require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

line#L604: require(totalFee <= price, "Total amount of fees are more than the price");

line#L630: require(to != address(0), "Transfer to zero address");

line#L636: require(success, "Pool transfer failed");

line#L641: revert("Invalid payment token");
```

### `x < y + 1` is cheaper than `x <= y`

[Pool.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol):

```solidity
line#L45:  require(_balances[msg.sender] >= amount);

line#L71:  require(_balances[from] >= amount);
```

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol):

```solidity
line#L274: sell.order.listingTime <= buy.order.listingTime ? sell.order.trader : buy.order.trader,

line#L543: if (sell.listingTime <= buy.listingTime) {

line#L573: require(remainingETH >= price);

line#L604: require(totalFee <= price, "Total amount of fees are more than the price");
```

### `++i` costs less gas than `i++` (same for `--i`/`i--`)

Prefix increments are cheaper than postfix increments

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol):

```solidity
line#L184: for (uint8 i = 0; i < executionsLength; i++) {

line#L307: for (uint8 i = 0; i < orders.length; i++) {

line#L598: for (uint8 i = 0; i < fees.length; i++) {
```

### Mark functions as `payable` to avoid the low-level evm `is-a-payment-provided` check

The EVM checks whether a payment is provided to the function and this check costs additional gas. If the function is marked as `payable` there is no such check

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol):

```solidity
line#L56:  function open() external onlyOwner {

line#L60:  function close() external onlyOwner {

line#L289: function cancelOrder(Order calldata order) public {
```

### Use `unchecked` for the counter in `for` loops

Since it is compared to a target value (usually the length of an array) there is no way the counter overflows

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol):

```solidity
line#L184: for (uint8 i = 0; i < executionsLength; i++) {

line#L307: for (uint8 i = 0; i < orders.length; i++) {

line#L598: for (uint8 i = 0; i < fees.length; i++) {
```

### Use `private` visibility modifiers instead of `public` for storage `constant`s

Saves 3000+ gas per variable when deploying contract

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol):

```solidity
line#L70:  string public constant NAME = "Exchange";

line#L71:  string public constant VERSION = "1.0";

line#L73:  address public constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;

line#L74:  address public constant POOL = 0xF66CfDf074D2FFD6A4037be3A669Ed04380Aef2B;
```

### Function inlining

If a function is called only once it can be inlined in order to save 20 gas

[Pool.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol):

```solidity
line#L70:  function _transfer(address from, address to, uint256 amount) private {
```

### Do not use strings longer than 32 bytes

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol):

```solidity
line#L49:  require(isInternal, "This function should not be called directly");

line#L295: require(!cancelledOrFilled[hash], "Order already cancelled or filled");

line#L604: require(totalFee <= price, "Total amount of fees are more than the price");
```

[Pool.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol):

```solidity
line#L63:  revert('Caller is not authorized to transfer');
```

### Do not write default values to variables

If you declare a variables of type `uint256` it will automatically get assigned to its default value of 0. It is a gas wastage to assign it yourself.

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol):

```solidity
line#L146: bool public isInternal = false;

line#L147: uint256 public remainingETH = 0;

line#L307: for (uint8 i = 0; i < orders.length; i++) {

line#L597: uint256 totalFee = 0;
```
