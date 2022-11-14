# QA Report
## Found [2]

### [Q-01] Require Statements Missing Error Message

Error messages help with troubleshooting, some require statements are missing error messages.

#### Findings:

*/contracts/Exchange.sol*
Line(s): [240](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L240), [291](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L291), [573](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573)
```solidity
240:	require(sell.order.side == Side.Sell);
291:	require(msg.sender == order.trader);
573:	require(remainingETH >= price);
```

*/contracts/Pool.sol*
Line(s): [45](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45), [48](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L48), [71](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71), [72](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L72)
```solidity
45:	require(_balances[msg.sender] >= amount);
48:	require(success);
71:	require(_balances[from] >= amount);
72:	require(to != address(0));
```

### [NC-02] State Variables Not Grouped Together

For better readability state variables should be grouped together at the top of the code, especially when functions / modifiers above call on the variables.

Line(s): [146](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L146), [147](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L147)

```solidity
146:	bool public isInternal = false;
147:	uint256 public remainingETH = 0;
```