
## Variables less than 256 bits

Due to how the EVM natively works on 256 bit numbers, using a 8 bit number in for-loops introduces additional costs as the EVM has to properly enforce the limits of this smaller type.

https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html

### Proof of concept

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L184

```solidity
    for (uint8 i = 0; i < executionsLength; i++) {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L307

```solidity
    for (uint8 i = 0; i < orders.length; i++) {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L598

```solidity
    for (uint8 i = 0; i < fees.length; i++) {
```

## Use function arguments instead of storage variables in emitting events

In event emits using local variables or function arguments instead of storage variable can avoid storage read and save gas

### Proof of concept

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L329

```solidity
        emit NewExecutionDelegate(executionDelegate);   
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L338

```solidity
        emit NewPolicyManager(policyManager); 
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L347

```solidity
        emit NewOracle(oracle);
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L355

```solidity
        emit NewBlockRange(blockRange); 
```

## Statements can be unchecked to save gas

When underflow/overflow is not possible, statements can be unchecked to avoid the underflow/overflow checks introduced in version 0.8+

### Proof of concept

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L573

```solidity
            require(remainingETH >= price);
            remainingETH -= price;  
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L604-L607

```solidity
        require(totalFee <= price, "Total amount of fees are more than the price");

        /* Amount that will be received by seller. */
        uint256 receiveAmount = price - totalFee; 
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L45-L46

```solidity
    require(_balances[msg.sender] >= amount);
    _balances[msg.sender] -= amount; 
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L71-L74

```solidity
    require(_balances[from] >= amount);
    require(to != address(0));
    _balances[from] -= amount;
    _balances[to] += amount;
```