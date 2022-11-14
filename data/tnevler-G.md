# Report
## Gas Optimizations ##
### [G-1]: The increment in for loop post condition can be made unchecked
**Context:**

1. ```for (uint8 i = 0; i < executionsLength; i++) {``` [L184](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184) 
1. ```for (uint8 i = 0; i < orders.length; i++) {``` [L307](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307) 
1. ```for (uint8 i = 0; i < fees.length; i++) {``` [L598](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598) 

**Description:**

[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**

Example how to fix. Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

### [G-2]: Place subtractions where the operands can't underflow in unchecked {} block
**Context:**

1. ```remainingETH -= price;``` [L574](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574) 
1. ```uint256 receiveAmount = price - totalFee;``` [L607](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L607) 
1. ```_balances[msg.sender] -= amount;``` [L46](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46) 
1. ```_balances[from] -= amount;``` [L73](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73) 

**Description:**

Some gas can be saved by using an unchecked {} block if an underflow isn't possible because of a previous require() or if-statement.
