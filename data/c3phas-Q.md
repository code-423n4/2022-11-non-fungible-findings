## QA
### Missing checks for address(0x0) when assigning values to address state variables

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L130
```solidity
File: /contracts/Exchange.sol
130:        oracle = _oracle;
```

### Use of ecrecover is vulnerable to signature malleability

Recommended to use ECDSA from openzeppallin.

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L516-L530
```solidity
File: /contracts/Exchange.sol
524:        address recoveredSigner = ecrecover(digest, v, r, s);
```


### Public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.
Functions marked by external use call data to read arguments, where public will first allocate in local memory and then load them.

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44-L50
```solidity
File: /contracts/Pool.sol
44:    function withdraw(uint256 amount) public {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L79
```solidity
File: /contracts/Pool.sol
79:    function balanceOf(address user) public view returns (uint256) {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L83
```solidity
File: /contracts/Pool.sol
83:    function totalSupply() public view returns (uint256) {
```

### `ecrecover` precompile internally checks if the value is 27 or 28. No need to check in the client side.
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L523
```solidity
File:/contracts/Exchange.sol
523:        require(v == 27 || v == 28, "Invalid v parameter");
```

[See this pull request on the Ethereum Yellow paper](https://github.com/ethereum/yellowpaper/pull/860)

### Open todos

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L18
```solidity
File: /contracts/Pool.sol
18:    // TODO: set proper address before deployment
```
### Missing Friendly revert strings
 require()/revert() statements should have descriptive reason strings.
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L240
```solidity
File: /contracts/Exchange.sol
240:        require(sell.order.side == Side.Sell);

291:        require(msg.sender == order.trader);
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L45
```solidity
File: /contracts/Pool.sol
45:        require(_balances[msg.sender] >= amount);

48:        require(success);

71:        require(_balances[from] >= amount);
72:        require(to != address(0));
```

### Delete commented code
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L176-L181
```solidity
File: /contracts/Exchange.sol
176:        uint256 executionsLength = executions.length;
177:        for (uint8 i=0; i < executionsLength; i++) {
178:            bytes memory data = abi.encodeWithSelector(this._execute.selector, executions[i].sell, executions[i].buy);
179:            (bool success,) = address(this).delegatecall(data);
180:        }
181:        _returnDust(remainingETH);
```

```diff

diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..e623eec 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -171,15 +171,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
         whenOpen
         setupExecution
     {
-        /*
-        REFERENCE
-        uint256 executionsLength = executions.length;
-        for (uint8 i=0; i < executionsLength; i++) {
-            bytes memory data = abi.encodeWithSelector(this._execute.selector, executions[i].sell, executions[i].buy);
-            (bool success,) = address(this).delegatecall(data);
-        }
-        _returnDust(remainingETH);
-        */
+
         uint256 executionsLength = executions.length;
         for (uint8 i = 0; i < executionsLength; i++) {
             assembly {
```

### Natspec is incomplete/Missing
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L323
```solidity
File: /contracts/Exchange.sol

//@audit: Missing @param _executionDelegate
323:    function setExecutionDelegate(IExecutionDelegate _executionDelegate)

//@audit: Missing @param _policyManager
332:    function setPolicyManager(IPolicyManager _policyManager)

//@audit: Missing @param _oracle
341:    function setOracle(address _oracle)

//@audit: Missing @param _blockRange
350:    function setBlockRange(uint256 _blockRange)

```

### Code Structure Deviates From Best-Practice
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. 
In the following https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol , some state variables are declared in between functions.

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L146-L147
```solidity
File: /contracts/Exchange.sol
146:    bool public isInternal = false;
147:    uint256 public remainingETH = 0;
```

 **Recommendation:**
Consider adopting recommended best-practice for code structure and layout.

See: [https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout)
