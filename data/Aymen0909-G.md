# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Use `unchecked` blocks to save gas  |  4 |
| 2      | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |   6 |
| 3      | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  3  |
| 4      | Functions guaranteed to revert when called by normal users can be marked `payable` |  6 |

## Findings

### 1- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 4 instances of this issue:

File: contracts/Exchange.sol [Line 574](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574)
```
remainingETH -= price;
```

The above operation cannot underflow due to the check [require(remainingETH >= price);](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573) and should be modified as follows :

```
unchecked {remainingETH -= price;}
```

File: contracts/Exchange.sol [Line 607](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L607)
```
uint256 receiveAmount = price - totalFee;
```

The above operation cannot underflow due to the check [require(totalFee <= price, "Total amount of fees are more than the price");](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L605) and should be modified as follows :

```
uint256 receiveAmount;
unchecked {receiveAmount = price - totalFee;}
```

File: contracts/Pool.sol [Line 46](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46)
```
_balances[msg.sender] -= amount;
```

The above operation cannot underflow due to the check [require(_balances[from] >= amount);](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45) and should be modified as follows :

```
unchecked {_balances[msg.sender] -= amount;}
```

File: contracts/Pool.sol [Line 73](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73)
```
_balances[from] -= amount;
```

The above operation cannot underflow due to the check [require(_balances[from] >= amount);](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71) and should be modified as follows :

```
unchecked {_balances[from] -= amount;}
```

### 2- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 6 instances of this issue:

File: contracts/Exchange.sol [Line 316](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316)
```
nonces[msg.sender] += 1;
```

File: contracts/Exchange.sol [Line 574](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574)
```
remainingETH -= price;
```

File: contracts/Pool.sol [Line 36](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L36)
```
_balances[msg.sender] += msg.value;
```

File: contracts/Pool.sol [Line 46](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46)
```
_balances[msg.sender] -= amount
```

File: contracts/Pool.sol [Line 73](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73)
```
_balances[from] -= amount;
```

File: contracts/Pool.sol [Line 74](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L74)
```
_balances[to] += amount;
```

### 3- ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 3 instances of this issue:

File: contracts/Exchange.sol [Line 184](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184)
```
for (uint8 i = 0; i < executionsLength; i++)
``` 

File: contracts/Exchange.sol [Line 307](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307)
```
for (uint8 i = 0; i < orders.length; i++) 
```

File: contracts/Exchange.sol [Line 598](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307)
```
for (uint8 i = 0; i < fees.length; i++)
```

### 4- Functions guaranteed to revert when called by normal users can be marked `payable` :

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 6 instances of this issue:

```
File: contracts/Exchange.sol

56       function open() external onlyOwner
60       function close() external onlyOwner
323      function setExecutionDelegate(IExecutionDelegate _executionDelegate) external onlyOwner
332      function setPolicyManager(IPolicyManager _policyManager) external onlyOwner
341      function setOracle(address _oracle) external onlyOwner
350      function setBlockRange(uint256 _blockRange) external onlyOwner
```
