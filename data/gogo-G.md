# 2022-11-NON-FUNGIBLE

## Gas Optimizations Report

### The usage of `++i` will cost less gas than `i++`. The same change can be applied to `i--` as well.

This change would save up to 6 gas per instance/loop.

_There are **3** instances of this issue:_

```solidity
File: contracts/Exchange.sol

184:  for (uint8 i = 0; i < executionsLength; i++) {

307:  for (uint8 i = 0; i < orders.length; i++) {

598:  for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

### `internal` and `private` functions that are called only once should be inlined.

The execution of a non-inlined function would cost up to 40 more gas because of two extra `jump`s as well as some other instructions.

_There are **1** instances of this issue:_

```solidity
File: contracts/Pool.sol

70:   function _transfer(address from, address to, uint256 amount) private {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

### It costs more gas to initialize non-`constant`/non-`immutable` variables to zero than to let the default of zero be applied

Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

_There are **6** instances of this issue:_

```solidity
File: contracts/Exchange.sol

146:  bool public isInternal = false;

147:  uint256 public remainingETH = 0;

184:  for (uint8 i = 0; i < executionsLength; i++) {

307:  for (uint8 i = 0; i < orders.length; i++) {

597:  uint256 totalFee = 0;

598:  for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

### Using `private` rather than `public` for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

_There are **5** instances of this issue:_

```solidity
File: contracts/Exchange.sol

70:   string public constant NAME = "Exchange";

71:   string public constant VERSION = "1.0";

72:   uint256 public constant INVERSE_BASIS_POINT = 10_000;

73:   address public constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;

74:   address public constant POOL = 0xF66CfDf074D2FFD6A4037be3A669Ed04380Aef2B;
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

### Functions that are access-restricted from most users may be marked as `payable`

Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **2** instances of this issue:_

```solidity
File: contracts/Exchange.sol

56:   function open() external onlyOwner {

60:   function close() external onlyOwner {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops

When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **3** instances of this issue:_

```solidity
File: contracts/Exchange.sol

184:  for (uint8 i = 0; i < executionsLength; i++) {

307:  for (uint8 i = 0; i < orders.length; i++) {

598:  for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

### Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead

'When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.' \ https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html \ Use a larger size then downcast where needed

_There are **6** instances of this issue:_

```solidity
File: contracts/Exchange.sol

184:  for (uint8 i = 0; i < executionsLength; i++) {

307:  for (uint8 i = 0; i < orders.length; i++) {

443:  uint8 v,

479:  uint8 v; bytes32 r; bytes32 s;

519:  uint8 v,

598:  for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **6** instances of this issue:_

```solidity
File: contracts/Exchange.sol

274:      sell.order.listingTime <= buy.order.listingTime ? sell.order.trader : buy.order.trader,

543:  if (sell.listingTime <= buy.listingTime) {

573:      require(remainingETH >= price);

604:  require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```solidity
File: contracts/Pool.sol

45:    require(_balances[msg.sender] >= amount);

71:    require(_balances[from] >= amount);
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

### `require()/revert()` strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

_There are **4** instances of this issue:_

```solidity
File: contracts/Exchange.sol

49:    require(isInternal, "This function should not be called directly");

295:  require(!cancelledOrFilled[hash], "Order already cancelled or filled");

604:  require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```solidity
File: contracts/Pool.sol

63:        revert('Caller is not authorized to transfer');
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

### Use custom errors rather than `revert()`/`require()` strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

_There are **21** instances of this issue:_

```solidity
File: contracts/Exchange.sol

36:    require(isOpen == 1, "Closed");

49:    require(isInternal, "This function should not be called directly");

245:  require(_validateSignatures(sell, sellHash), "Sell failed authorization");

246:  require(_validateSignatures(buy, buyHash), "Buy failed authorization");

248:  require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");

249:  require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");

295:  require(!cancelledOrFilled[hash], "Order already cancelled or filled");

327:  require(address(_executionDelegate) != address(0), "Address cannot be zero");

336:  require(address(_policyManager) != address(0), "Address cannot be zero");

345:  require(_oracle != address(0), "Address cannot be zero");

414:      require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

523:  require(v == 27 || v == 28, "Invalid v parameter");

545:      require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

549:      require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

552:  require(canMatch, "Orders cannot be matched");

604:  require(totalFee <= price, "Total amount of fees are more than the price");

630:      require(to != address(0), "Transfer to zero address");

632:      require(success, "ETH transfer failed");

636:      require(success, "Pool transfer failed");

641:      revert("Invalid payment token");
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```solidity
File: contracts/Pool.sol

63:        revert('Caller is not authorized to transfer');
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol
