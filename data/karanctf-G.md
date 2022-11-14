## 1. Use default values instead of Explicit initialization with `0`.
It is not required for variable declaration of numTokens because `uints are 0` by default.removeing this will reduce contract size and save a bit of gas.

```solidity
contracts/Exchange.sol:177:        for (uint8 i=0; i < executionsLength; i++) {
contracts/Exchange.sol:184:        for (uint8 i = 0; i < executionsLength; i++) {
contracts/Exchange.sol:307:        for (uint8 i = 0; i < orders.length; i++) {
contracts/Exchange.sol:597:        uint256 totalFee = 0;
contracts/Exchange.sol:598:        for (uint8 i = 0; i < fees.length; i++) {
```

## 2. Cache `.length` in loops
**Summary:**  Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

```solidity
contracts/Exchange.sol:176:        uint256 executionsLength = executions.length;
contracts/Exchange.sol:183:        uint256 executionsLength = executions.length;
contracts/Exchange.sol:307:        for (uint8 i = 0; i < orders.length; i++) {
contracts/Exchange.sol:412:        if (order.order.extraParams.length > 0 && order.order.extraParams[0] == 0x01) {
contracts/Exchange.sol:598:        for (uint8 i = 0; i < fees.length; i++) {
```
## 3. Use `!= 0` instead of `> 0`
```solidity
contracts/Exchange.sol:412:        if (order.order.extraParams.length > 0 && order.order.extraParams[0] == 0x01) {
```

## 4. `<x> += <y>` costs more gas than `<x> = <x> + <y>` 
```solidity
contracts/Exchange.sol:316:        nonces[msg.sender] += 1;
contracts/Exchange.sol:574:            remainingETH -= price;
contracts/Exchange.sol:601:            totalFee += fee;
contracts/Pool.sol:36:        _balances[msg.sender] += msg.value;
contracts/Pool.sol:46:        _balances[msg.sender] -= amount;
contracts/Pool.sol:73:        _balances[from] -= amount;
contracts/Pool.sol:74:        _balances[to] += amount;
```

## 5. Use `calldata` instead of `memory` when used only for reading
**forat virable declare time only
```solidity
contracts/Exchange.sol:178:            bytes memory data = abi.encodeWithSelector(this._execute.selector, executions[i].sell, executions[i].buy);
contracts/Exchange.sol:455:            (bytes32[] memory merklePath) = abi.decode(extraSignature, (bytes32[]));
contracts/Exchange.sol:500:            (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```

## 6. Use preincrement to save gas in loops
`++i` costs less gas than `i++`, especially when it is used in for-loops (--i/i-- too) Saves 5 gas PER LOOP
```solidity
contracts/Exchange.sol:177:        for (uint8 i=0; i < executionsLength; i++) {
contracts/Exchange.sol:184:        for (uint8 i = 0; i < executionsLength; i++) {
contracts/Exchange.sol:307:        for (uint8 i = 0; i < orders.length; i++) {
contracts/Exchange.sol:598:        for (uint8 i = 0; i < fees.length; i++) {
```

## 7.  Use `variable == false|0` -> `!variable` or `variable ==  true` -> `variable`
```solidity
contracts/Exchange.sol:412:        if (order.order.extraParams.length > 0 && order.order.extraParams[0] == 0x01) {
contracts/Exchange.sol:624:        if (amount == 0) {
```

## 8.` >=` COSTS LESS GAS THAN `>`
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde 
```solidity
contracts/Exchange.sol:275:            sell.order.listingTime > buy.order.listingTime ? sell.order.trader : buy.order.trader,
contracts/Exchange.sol:412:        if (order.order.extraParams.length > 0 && order.order.extraParams[0] == 0x01) {
```


## 9. Use `private` for `constants`
```solidity
contracts/Exchange.sol:70:    string public constant NAME = "Exchange";
contracts/Exchange.sol:71:    string public constant VERSION = "1.0";
contracts/Exchange.sol:72:    uint256 public constant INVERSE_BASIS_POINT = 10_000;
contracts/Exchange.sol:73:    address public constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;
contracts/Exchange.sol:74:    address public constant POOL = 0xF66CfDf074D2FFD6A4037be3A669Ed04380Aef2B;
```

## 10. Use custom errors to save deployment and runtime costs in case of revert
Instead of using strings for error messages (e.g., `require(msg.sender == owner, “unauthorized”)`), you can use custom errors to reduce both deployment and runtime gas costs. In addition, they are very convenient as you can easily pass dynamic information to them.
```solidity
contracts/Exchange.sol:36:        require(isOpen == 1, "Closed");
contracts/Exchange.sol:49:        require(isInternal, "This function should not be called directly");
contracts/Exchange.sol:240:        require(sell.order.side == Side.Sell);
contracts/Exchange.sol:245:        require(_validateSignatures(sell, sellHash), "Sell failed authorization");
contracts/Exchange.sol:246:        require(_validateSignatures(buy, buyHash), "Buy failed authorization");
contracts/Exchange.sol:248:        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
contracts/Exchange.sol:249:        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
contracts/Exchange.sol:291:        require(msg.sender == order.trader);
contracts/Exchange.sol:295:        require(!cancelledOrFilled[hash], "Order already cancelled or filled");
contracts/Exchange.sol:327:        require(address(_executionDelegate) != address(0), "Address cannot be zero");
contracts/Exchange.sol:336:        require(address(_policyManager) != address(0), "Address cannot be zero");
contracts/Exchange.sol:345:        require(_oracle != address(0), "Address cannot be zero");
contracts/Exchange.sol:414:            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
contracts/Exchange.sol:523:        require(v == 27 || v == 28, "Invalid v parameter");
contracts/Exchange.sol:545:            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
contracts/Exchange.sol:549:            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
contracts/Exchange.sol:552:        require(canMatch, "Orders cannot be matched");
contracts/Exchange.sol:573:            require(remainingETH >= price);
contracts/Exchange.sol:604:        require(totalFee <= price, "Total amount of fees are more than the price");
contracts/Exchange.sol:630:            require(to != address(0), "Transfer to zero address");
contracts/Exchange.sol:632:            require(success, "ETH transfer failed");
contracts/Exchange.sol:636:            require(success, "Pool transfer failed");
contracts/Pool.sol:45:        require(_balances[msg.sender] >= amount);
contracts/Pool.sol:48:        require(success);
contracts/Pool.sol:71:        require(_balances[from] >= amount);
contracts/Pool.sol:72:        require(to != address(0));
```

