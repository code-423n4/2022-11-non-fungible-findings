## 1. Custom error are cheaper than string messages

_contracts/Pool.sol:_ [L63](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L63)

_contracts/Exchange.sol:_ [L36](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L36)
[L49](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L49)
[L246](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L246)
[L295](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L295)
[L327](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L327)
[L336](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L336)
[L345](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L345)
[L414](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L414)
[L523](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L523)
[L545](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L545)
[L549](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L549)
[L552](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L552)
[L604](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L604)
[L636](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L636)

## 2. Place `i++` in an `unchecked` blocks in for-loops

_contracts/Exchange.sol:_ [L184](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184)
[L307](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307)
[L598](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598)

## 3. Consider marking functions as `payable` if there is no risk of sending value through them

### This change will save gas each time a function is called

_contracts/Pool.sol:_ [L15](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L15)

_contracts/Exchange.sol:_ [L60](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L60)
[L66](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L66)
[L289](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L289)

## 4. Prefix incrementing and decrementing costs around 6 gas less than the postfix ones

### e.g. ++var is cheaper than var++

_contracts/Exchange.sol:_ [L184](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184)
[L307](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307)
[L598](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598)

## 5. Use `x < y + 1` in stead of `x <= y`

_contracts/Exchange.sol:_ [L543](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L543)
[L573](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573)
[L604](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L604)

_contracts/Pool.sol:_ [L45](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45)
[L71](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71)

## 6. Function that are called only once can be inlined in the calling function

### This change will save around 30 gas units

_contracts/Pool.sol:_ [L70](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L70)

## 7. Explicitly assingning default values to variables is a waste of gas

### Use `uint256 i;` instead of `uint256 i = 0;`

_contracts/Exchange.sol:_ [L146](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L146)
[L147](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L147)
[L184](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184)
[L307](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307)
[L597](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L597)
