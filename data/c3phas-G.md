## FINDINGS
NB: *Some functions have been truncated where neccessary to just show affected parts of the code*
Throught the report some places might be denoted with audit tags to show the actual place affected.
 
### Tighly pack storage variables/optimize the order of variable declaration (From 8 SLOTS to 7 SLOTS)

Here, the storage variables can be tightly packed 

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L146
```solidity
File: /contracts/Exchange.sol

//@audit: move the bool isInternal next to address oracle
77:    /* Variables */
78:    IExecutionDelegate public executionDelegate;
79:    IPolicyManager public policyManager;
80:    address public oracle;
81:    uint256 public blockRange;

84:    /* Storage */
85:    mapping(bytes32 => bool) public cancelledOrFilled;
86:    mapping(address => uint256) public nonces;

145:   /* External Functions */
146:    bool public isInternal = false;
147:    uint256 public remainingETH = 0;
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..6fa9ede 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -78,6 +78,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
     IExecutionDelegate public executionDelegate;
     IPolicyManager public policyManager;
     address public oracle;
+    bool public isInternal = false;
     uint256 public blockRange;


@@ -143,7 +144,6 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U


     /* External Functions */
-    bool public isInternal = false;
     uint256 public remainingETH = 0;

     /**
```


### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L565-L582
```solidity
File: /contracts/Exchange.sol
565:    function _executeFundsTransfer(

572:        if (msg.sender == buyer && paymentToken == address(0)) {
573:            require(remainingETH >= price);
574:            remainingETH -= price;
575:        }
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..682a797 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -570,8 +570,9 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
         uint256 price
     ) internal {
         if (msg.sender == buyer && paymentToken == address(0)) {
-            require(remainingETH >= price);
-            remainingETH -= price;
+            uint256 _remainingETH = remainingETH;
+            require(_remainingETH >= price);
+            remainingETH =  _remainingETH - price;
         }
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44-L50
### Pool.sol.withdraw(): The value of \_balances[msg.sender] should be cached(saves 118 gas on average)

|        | Min   | Max   | Avg   |
| ------ | ----- | ----- | ----- |
| Before | - | - | 40433 |
| After  | - | - | 40315 |
|        |       |       |       |
```solidity
File: /contracts/Pool.sol
44:    function withdraw(uint256 amount) public {
45:        require(_balances[msg.sender] >= amount);
46:        _balances[msg.sender] -= amount;
```

```diff
diff --git a/contracts/Pool.sol b/contracts/Pool.sol
index 3722082..bac2c33 100644
--- a/contracts/Pool.sol
+++ b/contracts/Pool.sol
@@ -42,8 +42,9 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
      * @param amount Amount to withdraw
      */
     function withdraw(uint256 amount) public {
-        require(_balances[msg.sender] >= amount);
-        _balances[msg.sender] -= amount;
+        uint256 _senderBalance = _balances[msg.sender];
+        require(_senderBalance >= amount);
+        _balances[msg.sender] = _senderBalance - amount;
         (bool success,) = payable(msg.sender).call{value: amount}("");
         require(success);
         emit Transfer(address(0), msg.sender, amount);
```


https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L70-L77
### Pool.sol.\_transfer():  The value of \_balances[from] should be cached
```solidity
File:/contracts/Pool.sol
70:    function _transfer(address from, address to, uint256 amount) private {
71:        require(_balances[from] >= amount);
72:        require(to != address(0));
73:        _balances[from] -= amount;
```

```diff
diff --git a/contracts/Pool.sol b/contracts/Pool.sol
index 3722082..9b8a022 100644
--- a/contracts/Pool.sol
+++ b/contracts/Pool.sol
@@ -68,9 +68,12 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
     }

     function _transfer(address from, address to, uint256 amount) private {
-        require(_balances[from] >= amount);
+        uint256 _balanceFrom = _balances[from];
+        require(_balanceFrom >= amount);
         require(to != address(0));
-        _balances[from] -= amount;
+        unchecked {
+            _balances[from] = _balanceFrom - amount;
+        }
         _balances[to] += amount;

         emit Transfer(from, to, amount);
```



### Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L440-L448
```solidity
File: /contracts/Exchange.sol
440:    function _validateUserAuthorization(
441:        bytes32 orderHash,
442:        address trader,
443:        uint8 v,
444:        bytes32 r,
445:        bytes32 s,
446:        SignatureVersion signatureVersion,
447:        bytes calldata extraSignature
448:    ) internal view returns (bool) {
```

The above is only called once on [Line 399](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L399)

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L471-L476
```solidity
File: /contracts/Exchange.sol
471:    function _validateOracleAuthorization(
472:        bytes32 orderHash,
473:        SignatureVersion signatureVersion,
474:        bytes calldata extraSignature,
475:        uint256 blockNumber
476:    ) internal view returns (bool) {
```

The above function is only called once on [Line 416](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L416)


https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L537-L541
```solidity
File: /contracts/Exchange.sol
537:    function _canMatchOrders(Order calldata sell, Order calldata buy)
538:        internal
539:        view
540:        returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)
541:
{
```
The above is only called once on [Line 251](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L251)

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L565-L571
```solidity
File: /contracts/Exchange.sol
565:    function _executeFundsTransfer(
566:        address seller,
567:        address buyer,
568:        address paymentToken,
569:        Fee[] calldata fees,
570:        uint256 price
571:    ) internal {
```

The above is only called once on [Line 257](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L257)


https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L591-L596
```solidity
File: /contracts/Exchange.sol
591:    function _transferFees(
592:        Fee[] calldata fees,
593:        address paymentToken,
594:        address from,
595:        uint256 price
596:    ) internal returns (uint256) {
```

The above is only called once on [Line 578](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L578)

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L653-L660
```solidity
File: /contracts/Exchange.sol
653:    function _executeTokenTransfer(
654:        address collection,
655:        address from,
656:        address to,
657:        uint256 tokenId,
658:        uint256 amount,
659:        AssetType assetType
660:    ) internal {
```

The above function is being called only once on [Line 264](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L264)


### Emitting storage values instead of the memory one.
Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L323-L330
```solidity
File: /contracts/Exchange.sol
323:    function setExecutionDelegate(IExecutionDelegate _executionDelegate)
324:        external
325:        onlyOwner
326:    {
327:        require(address(_executionDelegate) != address(0), "Address cannot be zero");
328:        executionDelegate = _executionDelegate;
329:        emit NewExecutionDelegate(executionDelegate);
330:    }
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..dbe7861 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -326,7 +326,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
     {
         require(address(_executionDelegate) != address(0), "Address cannot be zero");
         executionDelegate = _executionDelegate;
-        emit NewExecutionDelegate(executionDelegate);
+        emit NewExecutionDelegate(_executionDelegate);
     }

```


https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L332-L339
```solidity
File: /contracts/Exchange.sol
332:    function setPolicyManager(IPolicyManager _policyManager)
333:        external
334:        onlyOwner
335:    {
336:        require(address(_policyManager) != address(0), "Address cannot be zero");
337:        policyManager = _policyManager;
338:        emit NewPolicyManager(policyManager);
339:    }
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..ab17632 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -335,7 +335,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
     {
         require(address(_policyManager) != address(0), "Address cannot be zero");
         policyManager = _policyManager;
-        emit NewPolicyManager(policyManager);
+        emit NewPolicyManager(_policyManager);
     }
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L341-L348
```solidity
File: /contracts/Exchange.sol
341:    function setOracle(address _oracle)
342:        external
343:        onlyOwner
344:    {
345:        require(_oracle != address(0), "Address cannot be zero");
346:        oracle = _oracle;
347:        emit NewOracle(oracle);
348:    }
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..5b7cfb1 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -344,7 +344,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
     {
         require(_oracle != address(0), "Address cannot be zero");
         oracle = _oracle;
-        emit NewOracle(oracle);
+        emit NewOracle(_oracle);
     }
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L350-L356
```solidity
File: /contracts/Exchange.sol
350:    function setBlockRange(uint256 _blockRange)
351:        external
352:        onlyOwner
353:    {
354:        blockRange = _blockRange;
355:        emit NewBlockRange(blockRange);
356:    }
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..34144dd 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -352,7 +352,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
         onlyOwner
     {
         blockRange = _blockRange;
-        emit NewBlockRange(blockRange);
+        emit NewBlockRange(_blockRange);
     }
```

### x += y costs more gas than x = x + y for state variables

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L574
```solidity
File: /contracts/Exchange.sol
574:            remainingETH -= price;
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..0fe488f 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -571,7 +571,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
     ) internal {
         if (msg.sender == buyer && paymentToken == address(0)) {
             require(remainingETH >= price);
-            remainingETH -= price;
+            remainingETH = remainingETH - price;
         }

```


https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L315-L318
### Exchange.sol.incrementNonce(): Save 114 gas on average
|        | Min | Max   | Avg   | 
| ------ | --- | ------- | ----- | 
| Before | 32939| 50039 | 41489
| After  | 32835| 49935 | 41385

```solidity
File: /contracts/Exchange.sol
315:    function incrementNonce() external {
316:        nonces[msg.sender] += 1;
317:        emit NonceIncremented(msg.sender, nonces[msg.sender]);
318:    }
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..bf5f114 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -313,7 +313,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
      * @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order
      */
     function incrementNonce() external {
-        nonces[msg.sender] += 1;
+        nonces[msg.sender] = nonces[msg.sender] + 1;
         emit NonceIncremented(msg.sender, nonces[msg.sender]);
     }

```

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L607
```solidity
File: /contracts/Exchange.sol
607:        uint256 receiveAmount = price - totalFee;
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..7f6467e 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -604,7 +604,11 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
         require(totalFee <= price, "Total amount of fees are more than the price");

         /* Amount that will be received by seller. */
-        uint256 receiveAmount = price - totalFee;
+        uint256 receiveAmount;
+        unchecked {
+            receiveAmount = price - totalFee;
+        }
+
         return (receiveAmount);
     }
```

The operation `price - totalFee` cannot underflow due to a check on [Line 604](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L604) that ensures that the value of `price` is greater than `totalFee` prior to perfoming the subtraction

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L46
### Pool.sol.withdraw(): using unchecked saves 87 gas

|        | Min   | Max   | Avg   |
| ------ | ----- | ----- | ----- |
| Before | - | - | 40433 |
| After  | - | - | 40346 |
|        |       |       |       |
```solidity
File: /contracts/Pool.sol
46:        _balances[msg.sender] -= amount;
```

```diff
diff --git a/contracts/Pool.sol b/contracts/Pool.sol
index 3722082..28a641a 100644
--- a/contracts/Pool.sol
+++ b/contracts/Pool.sol
@@ -43,7 +43,9 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
      */
     function withdraw(uint256 amount) public {
         require(_balances[msg.sender] >= amount);
-        _balances[msg.sender] -= amount;
+        unchecked {
+            _balances[msg.sender] -= amount;
+        }
         (bool success,) = payable(msg.sender).call{value: amount}("");
         require(success);
         emit Transfer(address(0), msg.sender, amount);
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L73
```solidity
File: /contracts/Pool.sol
73:        _balances[from] -= amount;
```

```diff
diff --git a/contracts/Pool.sol b/contracts/Pool.sol
index 3722082..73d2c0f 100644
--- a/contracts/Pool.sol
+++ b/contracts/Pool.sol
@@ -70,7 +70,9 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
     function _transfer(address from, address to, uint256 amount) private {
         require(_balances[from] >= amount);
         require(to != address(0));
-        _balances[from] -= amount;
+        unchecked {
+            _balances[from] -= amount;
+        }
         _balances[to] += amount;

         emit Transfer(from, to, amount);
```

The operation `_balances[from] -= amount` cannot underflow due to the check on [Line 71](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L71) that ensures that `_balances[from]` is greater than `amount` before performing the operation

### Using unchecked blocks to save gas - Increments in for loop can be unchecked  
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L184-L208
### Exchange.sol.bulkExecute(): using unchecked saves (385 gas on average)

|        | Min | Max   | Avg   | 
| ------ | --- | ------- | ----- | 
| Before | 796257| 902099 | 852656
| After  | 795872| 901714 | 852271


```solidity
File: /contracts/Exchange.sol
184:        for (uint8 i = 0; i < executionsLength; i++) {

208:        }
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..ef4dcdc 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -181,7 +181,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
         _returnDust(remainingETH);
         */
         uint256 executionsLength = executions.length;
-        for (uint8 i = 0; i < executionsLength; i++) {
+        for (uint8 i = 0; i < executionsLength;) {
             assembly {
                 let memPointer := mload(0x40)

@@ -205,6 +205,9 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
                 // must be put in delegatecall to maintain the authorization from the caller
                 let result := delegatecall(gas(), address(), memPointer, add(size, 0x04), 0, 0)
             }
+            unchecked {
+                ++i;
+            }
         }
         _returnDust();
     }
```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L307-L309
### Exchange.sol.cancelOrders(): Using unchecked saves (164 gas on average)

|        | Min | Max   | Avg   | 
| ------ | --- | ------- | ----- | 
| Before | -| - | 95362
| After  | -| - | 95198

```solidity
File: /contracts/Exchange.sol
307:        for (uint8 i = 0; i < orders.length; i++) {
308:            cancelOrder(orders[i]);
309:        }
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..0d4eadb 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -304,8 +304,11 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
      * @param orders Orders to cancel
      */
     function cancelOrders(Order[] calldata orders) external {
-        for (uint8 i = 0; i < orders.length; i++) {
+        for (uint8 i = 0; i < orders.length;) {
             cancelOrder(orders[i]);
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L598-L602

```solidity
File: /contracts/Exchange.sol
598:        for (uint8 i = 0; i < fees.length; i++) {
599:            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
600:            _transferTo(paymentToken, from, fees[i].recipient, fee);
601:            totalFee += fee;
602:        }
```

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..c098472 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -595,10 +595,13 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
         uint256 price
     ) internal returns (uint256) {
         uint256 totalFee = 0;
-        for (uint8 i = 0; i < fees.length; i++) {
+        for (uint8 i = 0; i < fees.length;) {
             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
             _transferTo(paymentToken, from, fees[i].recipient, fee);
             totalFee += fee;
+            unchecked {
+                ++i;
+            }
         }
```

[see resource](https://github.com/ethereum/solidity/issues/10695)

### A modifier used only once and not being inherited should be inlined to save gas

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L48-L51
```solidity
File: /contracts/Exchange.sol
48:    modifier internalCall() {
49:        require(isInternal, "This function should not be called directly");
50:        _;
51:    }
```

### Use shorter revert strings(less than 32 bytes) 
Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.Each extra chunk of byetes past the original 32 incurs an MSTORE which costs 3 gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L49
```solidity
File:/contracts/Exchange.sol
49:        require(isInternal, "This function should not be called directly");

295:        require(!cancelledOrFilled[hash], "Order already cancelled or filled");

604:        require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L63
```solidity
File: /contracts/Pool.sol
63:            revert('Caller is not authorized to transfer');
```
I suggest shortening the revert strings to fit in 32 bytes, or using custom errors.


