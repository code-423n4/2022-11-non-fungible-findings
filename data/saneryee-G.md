# Gas Optimizations

|         | Issue                                                                                 | Instances |
| ------- | :------------------------------------------------------------------------------------ | :-------: |
| [G-001] | Use Assembly to check for address(0)                                                  |     6     |

## [G-001] Use Assembly to check for address(0)

### Description:

Saves 6 gas per instance if using assemlby to check for address(0)

### Findings:

Total:6

[Exchange.sol#L327](https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L327)

```
327:    require(address(_executionDelegate) != address(0), "Address cannot be zero");
```

[Exchange.sol#L336](https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L336)

```
336:    require(address(_policyManager) != address(0), "Address cannot be zero");
```

[Exchange.sol#L345](https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L345)

```
345:    require(_oracle != address(0), "Address cannot be zero");
```

[Exchange.sol#L373](https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L373)

```
373:    (order.trader != address(0)) &&
```

[Exchange.sol#L630](https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L630)

```
630:    require(to != address(0), "Transfer to zero address");
```

[Pool.sol#L72](https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L72)

```
72:    require(to != address(0));
```
