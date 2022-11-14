# Gas Report
## Optimizations found [4]

### [G-01] ++i is cheaper than (i++)/(i+=1) [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-12-i-costs-less-gas-compared-to-i-or-i--1)

#### Findings:

*/contracts/Exchange.sol*
Line(s): [316](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316)
```solidity
316:	nonces[msg.sender] += 1;
```
**suggested change**
```solidity
316:	++nonces[msg.sender];
```


### [G-02] Use nested if instead of && [ⓘ](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#6--use-nested-if-and-avoid-multiple-check-combinations)

#### Findings:

*/contracts/Exchange.sol*
Line(s): [412](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L412), [572](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L572)
```solidity
412:	if (order.order.extraParams.length > 0 && order.order.extraParams[0] == 0x01) {
572:	if (msg.sender == buyer && paymentToken == address(0)) {
```
**suggested change**
```solidity
412:	if (order.order.extraParams.length > 0) {
			if (order.order.extraParams[0] == 0x01) {
	
			}
		}
572:	if (msg.sender == buyer) {
			if (paymentToken == address(0)) {

			}
		}
```


### [G-03] Unless Used for Variable Packing uint8 May Be More Expensive Than Using uint256 [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-15-usage-of-uint8-may-increase-gas-cost)

#### Findings:

*/contracts/Exchange.sol*
Line(s): [184](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184), [307](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307), [598](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598)
```solidity
184:	for (uint8 i = 0; i < executionsLength; i++) {
307:	for (uint8 i = 0; i < orders.length; i++) {
598:	for (uint8 i = 0; i < fees.length; i++) {
```
**suggested change**
```solidity
184:	for (uint256 i; i < executionsLength; ++i) {
307:	for (uint256 i; i < orders.length; ++i) {
598:	for (uint256 i; i < fees.length; ++i) {
```

**Further Savings IF the above is implemented:** If `i` is changed to a `uint256` the increment can be `unchecked` to save more gas.

example:
**from**
```solidity
for (uint256 i; i < amountOfTokens; ++i) {
	//Code
}
```
**to** 
```solidity
for (uint256 i; i < amountOfTokens;) {
	//Code
	unchecked {
		++i;
	}
}
```

### [G-04] Use `unchecked` For Arithmetic That Cannot Overflow 

Arithmetic is performed that cannot overflow / underflow based on a previous require. Adding an `unchecked` brace around these occurances will save gas (Solidity will not force an underflow / overflow).

**NOTE:** Findings show the check before each line that allows the second line to be `unchecked`.  Only the line following the check should be unchecked (NOT the check itself).

```solidity
unchecked {
	//Code
}
```

#### Findings:

*/contracts/Pool.sol*
Line(s): [45-46](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45-L46), [71](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71)/[73](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73)

```solidity
45:	require(_balances[msg.sender] >= amount);
46:	_balances[msg.sender] -= amount;
```

```solidity
71:	require(_balances[from] >= amount);
73:	_balances[from] -= amount;
```

*/contracts/Exchange.sol*
Line(s): [573-574](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573-L574), [604](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L604)/[607](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L607)

```solidity
604:	require(totalFee <= price, "Total amount of fees are more than the price");
607:	uint256 receiveAmount = price - totalFee;
```