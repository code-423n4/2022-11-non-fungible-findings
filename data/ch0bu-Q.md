# NON-CRITICAL

## 1. Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

```
18       // TODO: set proper address before deployment
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol


## 2. Consider adding checks for signature malleability

Use OpenZeppelin’s `ECDSA` contract rather than calling `ecrecover()` directly

```
524      address recoveredSigner = ecrecover(digest, v, r, s);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol


## 3. Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

```
90	event OrdersMatched(
91		address indexed maker,
92		address indexed taker,
93		Order sell,
94		bytes32 sellHash,
95		Order buy,
96		bytes32 buyHash
97	);
...
99	event OrderCancelled(bytes32 hash);
100	event NonceIncremented(address indexed trader, uint256 newNonce);
105	event NewBlockRange(uint256 blockRange);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol
```
23	event Transfer(address indexed from, address indexed to, uint256 amount);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol


## 4. `require()`/`revert()` statements should have descriptive reason strings

```
240	require(sell.order.side == Side.Sell);
291	require(msg.sender == order.trader);
573	require(remainingETH >= price);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol
```
45	require(_balances[msg.sender] >= amount);
48	require(success);
71	require(_balances[from] >= amount);
72	require(to != address(0));
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

