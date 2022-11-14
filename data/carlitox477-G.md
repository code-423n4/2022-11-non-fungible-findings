# Pool#withdraw can reduce one state variable access
```_balances[msg.sender]``` can reduce one state variable access. Replace [these lines](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L45-L46) for:
```solidity
uitn256 _balance = _balances[msg.sender]
require(_balance >= amount);
_balance -= amount;
 _balances[msg.sender] = _balance
```

# Pool#transferFrom can use a custom error to reduce gas consumption
[This line](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L63) can be replaced by something like ```revert(ERROR_UNAUTORIZED_TRANSFER());``` 

Note: Notice that this error was not caught by c4audit

# Pool#_transfer can reduce one state variable access
```_balances[from] ``` is accessed three times. This can be reduced to just 2 by replacing [these lines](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L71-L74) for:
```
uint256 fromBalance = _balances[from];
require(fromBalance >= amount);
require(to != address(0));
fromBalance -= amount;
_balances[from] = fromBalance
_balances[to] += amount;
```

# Exchange#_executeFundsTransfer can reduce one state variable access
```remainingETH``` is accessed 3 times. One access can be avoided by replacing [these lines](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L573-L574) for 

```solidity
uint256 _remainingETH = remainingETH;
require(_remainingETH >= price);
_remainingETH -= price;
remainingETH = _remainingETH;
```

# Exchange#setBlockRange can reduce one state variable access
```blockRange``` is accessed 2 times. This can be reduced to just 1 by replacing [this lines](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L355) for ```emit NewBlockRange(_blockRange);```

# Exchange#setOracle can reduce one state variable access
```oracle``` is accessed 2 times. This can be reduced to just 1 by replacing [this line](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L347) for ```emit NewOracle(_oracle);```

# Exchange#setPolicyManager can reduce one state variable access
```policyManager``` is accessed 2 times. This can be reduced to just 1 by replacing [this line](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L338) for ```emit NewPolicyManager(_policyManager);```

# Exchange#setExecutionDelegate can reduce one state variable access
```executionDelegate``` is accessed 2 times. This can be reduced to just 1 by replacing [this line](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L329) for ```emit NewExecutionDelegate(_executionDelegate);```

# Exchange#incrementNonce can reduce one state variable access
```nonces[msg.sender] ``` is accessed 3 times. This can be reduced to just 1 by replacing [these lines](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L316-L317) for:

```solidity
nonce = nonces[msg.sender] + 1
nonces[msg.sender] = nonce;
emit NonceIncremented(msg.sender, nonce);
```