## Open TODOs
```solidity
contracts/Pool.sol:18:    // TODO: set proper address before deployment
```
##  Use of `Block.timestamp`
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
```solidity
contracts/Exchange.sol:377:            (order.listingTime < block.timestamp) &&
contracts/Exchange.sol:378:            (block.timestamp < order.expirationTime)
```




## `require()`/`revert()` statements should have descriptive reason strings
```solidity
contracts/Exchange.sol:240:        require(sell.order.side == Side.Sell);
contracts/Exchange.sol:291:        require(msg.sender == order.trader);
contracts/Exchange.sol:573:            require(remainingETH >= price);
contracts/Pool.sol:45:        require(_balances[msg.sender] >= amount);
contracts/Pool.sol:48:        require(success);
contracts/Pool.sol:71:        require(_balances[from] >= amount);
contracts/Pool.sol:72:        require(to != address(0));
```

## Use `allowlist/denylist` rather than `blacklist/whitelist`
```solidity
contracts/Exchange.sol:545:            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
contracts/Exchange.sol:549:            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

