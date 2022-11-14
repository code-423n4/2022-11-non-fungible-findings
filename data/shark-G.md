## Return named variables to save gas

You can have further gas savings by using named return values as a temporary local variable.

For example:

File: `Pool.sol` [Line 83-85](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L83-L85)

```
    function totalSupply() public view returns (uint256) {
        return address(this).balance;
    }
```

Instead of the above code, you can return a named variable like so:

```
    function totalSupply() public view returns (uint256 supply) {
        supply = address(this).balance;
    }
```

Here are the rest of the instances found:

File: `Exchange.sol` [Line 369](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L369)
File: `Exchange.sol` [Line 390](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L390)
File: `Exchange.sol` [Line 448](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L448)
File: `Exchange.sol` [Line 476](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L476)
File: `Exchange.sol` [Line 522](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L522)
File: `Exchange.sol` [Line 540](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L540)
File: `Exchange.sol` [Line 596](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L596)
File: `Pool.sol` [Line 60](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L60)

## `x = x + y` is more efficient than `x += y` (same for subtracting)

Using `+` instead of `+=` saves around 113 gas.

_There are 7 instances of this issue:_

File: [`Exchange.sol`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol) ([Line 316](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316), [Line 574](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574), [Line 601](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L601))

```
File: contracts/Exchange.sol

316:        nonces[msg.sender] += 1;

574:            remainingETH -= price;

601:            totalFee += fee;
```

File: [`Pool.sol`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol) ([Line 36](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L36), [Line 46](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46), [Line 73](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73), [Line 74](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L74))

```
File: contracts/Pool.sol

36:        _balances[msg.sender] += msg.value;

46:        _balances[msg.sender] -= amount;

73:        _balances[from] -= amount;
74:        _balances[to] += amount;
```

## Unnecessary variable cache

The following instance of state variable cache is unnecessary because `remainingETH` is referenced only once in the function.

File: `Exchange.sol` [Line 213](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L213)

```
212    function _returnDust() private {
213        uint256 _remainingETH = remainingETH;
214        assembly {
215            if gt(_remainingETH, 0) {
216                let callStatus := call(
217                    gas(),
218                    caller(),
219                    selfbalance(),
220                    0,
221                    0,
222                    0,
223                    0
224                )
225            }
226        }
227    }
```

## Function Order Affects Gas Consumption

The order of the functions will have an impact on gas consumption. The reason why is that the order is dependent on the Method ID. Each function position will use up an extra 22 gas.

For more info visit [this medium article](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)
