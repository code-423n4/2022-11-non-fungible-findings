# Index
1. Require / Revert strings longer than 32 bytes cost extra gas
2. I = I + (-) X is cheaper in gas cost than I += (-=) X
3. Use unchecked in for/while loops when it's not possible to overflow
4. Modifiers that are used just once waste gas
5. Use unchecked in operation that can not be overflow/underflow
6. Remove variables that are used once
7. Public functions not used inside the contract should be external

# Details
## 1. Require / Revert strings longer than 32 bytes cost extra gas
### Description
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

### Lines in the code
[Exchange.sol#L49](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L49)
[Exchange.sol#L295](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L295)
[Exchange.sol#L604](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L604)
[Pool.sol#L63](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L63)

## 2. I = I + (-) X is cheaper in gas cost than I += (-=) X
### Description
In the following example (optimizer = 10000) it's possible to demostrate that I = I + X cost less gas than I += X in state variable.

```solidity
contract Test_Optimization {
    uint256 a = 1;
    function Add () external returns (uint256) {
        a = a + 1;
        return a;
    }
}

contract Test_Without_Optimization {
    uint256 a = 1;
    function Add () external returns (uint256) {
        a += 1;
        return a;
    }
}
```
* Test_Optimization cost is 26324 gas
* Test_Without_Optimization cost is 26440 gas

With this optimization it's possible to **save 116 gas**

### Lines in the code
[Exchange.sol#L316](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L316)
[Exchange.sol#L574](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L574)
[Exchange.sol#L601](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L601)
[Pool.sol#L36](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L36)
[Pool.sol#L46](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L46)
[Pool.sol#L73](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L73)
[Pool.sol#L74](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L74)

## 3. Use unchecked in for/while loops when it's not possible to overflow
### Description
Use unchecked { i++; } or unchecked{ ++i; } when it's not possible to overflow to save gas.

### Lines in the code
[Exchange.sol#L177](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L177)
[Exchange.sol#L184](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L184)
[Exchange.sol#L307](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L307)
[Exchange.sol#L598](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L598)

## 4. Modifiers that are used just once waste gas
### Description
It's possible to save gas (due to JUMP operation) removing the modifier that are used just once in the code and inlined the code.

### Lines in the code
[Exchange.sol#L48-L51](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L48-L51)

## 5. Use unchecked in operation that can not be overflow/underflow
### Description
Operations that could not be overflow/underflow due to an if or require could be unchecked to save gas.

### Lines in the code
[Exchange.sol#L574](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L574)
[Exchange.sol#L607](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L607)
[Pool.sol#L46](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L46)
[Pool.sol#L73](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L73)

## 6. Remove variables that are used once
### Description
Local variables that are used once could be removed.

```diff
-	uint256 receiveAmount = price - totalFee;
-	return (receiveAmount);
+	return (price - totalFee);
```
[Exchange.sol#L607-L608](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L607-L608)

```diff
-	bool success = IPool(POOL).transferFrom(from, to, amount);
-	require(success, "Pool transfer failed");
+	require(IPool(POOL).transferFrom(from, to, amount), "Pool transfer failed");
```
[Exchange.sol#L635-L636](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L635-L636)

## 7. Public functions not used inside the contract should be external
### Description
Public functions needs to write all of the arguments to memory is that public functions may be called internally, which is an entirely different
process than external calls. Internal calls are executed via jumps in the code and when the compiler generates the code for an internal function is 
expected to have its arguments located in memory.

For external functions, the compiler doesn't need to allow internal calls, and so it allows arguments to be read directly from calldata, saving the copying step.

### Lines in the code
[Pool.sol#L44](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44)
[Pool.sol#L58](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L58)
[Pool.sol#L79](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L79)
[Pool.sol#L83](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L83)

