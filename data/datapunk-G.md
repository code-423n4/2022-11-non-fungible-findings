G1: in [_transfer](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L70), the following 
```
        _balances[from] -= amount; 
        _balances[to] += amount;

```
can be put inside of unchecked
```
        unchecked {
            _balances[from] -= amount;
            _balances[to] += amount;
        }
```

G2. in [withdraw](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L46)
`_balances[msg.sender] -= amount;` can be inside of `unchecked {}` due to prior `require(_balances[msg.sender] >= amount);` 